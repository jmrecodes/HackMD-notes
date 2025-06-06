# Part 7: The Future - Scaling Your Application Beyond the Free Tier

[![hackmd-github-sync-badge](https://hackmd.io/@jmrecodes/SJt2D3x7le/badge)](https://hackmd.io/@jmrecodes/SJt2D3x7le)

The `e2-micro` instance is fantastic for getting started, but what happens when your application becomes a success? The "High CPU" alert you configured in Part 6 will start firing, and users will notice slowdowns. This is a good problem to have! This is where the true power and elasticity of the cloud come into play.

[<< Back to Google Cloud Index](deploying_a_secure_and_scalable_laravel_app_on_google_clouds_index.md)

[<<< Back to Knowledge Base Index](/README.md) | [Go to Part 6 >>](part_6_post-deployment_-_monitoring_logging_and_maintenance.md)

---

### Scaling Up vs. Scaling Out

There are two fundamental ways to scale a web application:

1.  **Vertical Scaling (Scaling Up):** You make your single server more powerful. It's like moving from a 4-cylinder car to a V8.
    *   **How:** Stop the VM, change its machine type from `e2-micro` to `e2-standard-4`, and start it again.
    *   **Pros:** It's extremely simple. It requires zero changes to your application code.
    *   **Cons:** There's a limit to how big one server can get. It requires downtime. You still have a single point of failure (if that one server dies, your site is down). It can be very expensive.

2.  **Horizontal Scaling (Scaling Out):** You add more, identical servers to share the load. It's like going from one 4-cylinder car to having ten of them.
    *   **How:** You put multiple VMs behind a load balancer, which distributes traffic among them.
    *   **Pros:** Virtually limitless scale. Highly resilient to failure (if one server dies, the load balancer just sends traffic to the others). More cost-effective for handling variable traffic because you can add/remove servers automatically.
    *   **Cons:** It's more complex to set up and, crucially, **it requires your application to be stateless.**

**This guide will focus on Horizontal Scaling, as it is the proper, cloud-native way to build modern applications.**

### The Golden Rule of Horizontal Scaling: Statelessness

This is the most important concept to grasp. For multiple servers to work together seamlessly, your application must be **stateless**. This means that **any server must be able to handle any request from any user at any time.**

A state*ful* application stores data on the local server disk that is needed for subsequent requests. Consider these two classic problems:

1.  **Session State:** By default, Laravel stores session information (like who is logged in) in files inside `storage/framework/sessions/`. If User A logs in on Server 1, their session file is on Server 1's disk. If their next page load is handled by Server 2, Server 2 has no idea who they are, and they are logged out. **This breaks the application.**
2.  **User-Uploaded Files:** If a user uploads a profile picture, it gets saved to Server 1's `storage/app/public/` directory. If they then visit a page that is rendered by Server 2, Server 2 can't find the image file, resulting in a broken image link. **This breaks the application.**

To scale horizontally, we must **externalize this state** into a centralized service that all servers can share.

#### Solution 1: Centralizing Sessions with Redis
We will use a managed, in-memory data store like Redis. It's extremely fast and perfect for session handling. GCP's managed service is called **Cloud Memorystore**.

1.  **(Local Terminal)** Provision a small Redis instance (this is a paid service, use your Free Trial credits).
    ```bash
    gcloud services enable redis.googleapis.com
    gcloud redis instances create laravel-sessions --size=1 --region=us-west1
    ```
2.  Get its IP address and port:
    ```bash
    gcloud redis instances describe laravel-sessions --region=us-west1 --format="value(host, port)"
    ```
3.  **(SSH into your VM)** Install the PHP Redis extension:
    ```bash
    sudo apt install -y php8.1-redis
    ```
4.  **(On VM)** Install the `predis` package for Laravel:
    ```bash
    cd /var/www/your-laravel-app
    sudo composer require predis/predis
    ```
5.  Update your `config/database.php` to include the Redis client setting.
6.  Update your `.env` file (or better yet, add these to your secret in Secret Manager!) with the Redis host/port and change the session driver:
    ```
    SESSION_DRIVER=redis
    REDIS_CLIENT=predis
    REDIS_HOST=THE_IP_FROM_STEP_2
    REDIS_PASSWORD=null
    REDIS_PORT=6379
    ```

#### Solution 2: Centralizing Files with Cloud Storage
We will use an object storage bucket. The best service for this is **Google Cloud Storage**.

1.  **(Local Terminal)** Create a globally unique bucket:
    ```bash
    gcloud storage buckets create gs://your-unique-laravel-uploads-bucket-name --location=US-WEST1
    ```
2.  Make the bucket publicly readable (so users can see the images):
    ```bash
    gcloud storage buckets add-iam-policy-binding gs://your-unique-laravel-uploads-bucket-name --member=allUsers --role=roles/storage.objectViewer
    ```
3.  **(On VM)** Install the Flysystem adapter for Google Cloud Storage:
    ```bash
    cd /var/www/your-laravel-app
    sudo composer require google/cloud-storage
    ```
4.  Update your `.env` file (or secret) to use the new `gcs` disk:
    ```
    FILESYSTEM_DISK=gcs
    GOOGLE_CLOUD_PROJECT_ID=your-gcp-project-id
    GOOGLE_CLOUD_STORAGE_BUCKET=your-unique-laravel-uploads-bucket-name
    ```
    Now, when you use `Storage::disk('public')->put(...)`, Laravel will automatically upload the file to your Cloud Storage bucket instead of the local disk.

### The Architecture of Scale: Load Balancers and Instance Groups

Now that our application is stateless, we can build the scalable infrastructure.

**The Plan:**
1.  Create a "blueprint" of our configured server, called an **Instance Template**.
2.  Create a **Managed Instance Group (MIG)** from this template. This group's job is to keep a certain number of VMs running.
3.  Create a **Health Check** so the system knows if a server is healthy.
4.  Create a **Load Balancer** to distribute traffic to the healthy VMs in the MIG.
5.  Enable **Autoscaling** on the MIG.

**(All commands in your LOCAL terminal)**

1.  **Create the Instance Template:** This creates a template based on our existing, fully configured `laravel-server`.
    ```bash
    gcloud compute instance-templates create laravel-template \
        --source-instance=laravel-server \
        --source-instance-zone=us-west1-a
    ```
2.  **Create a Health Check:** The load balancer will ping this path. If it gets a `200 OK` response, it knows the server is healthy.
    ```bash
    gcloud compute health-checks create http laravel-health-check --port 80 --request-path=/
    ```
3.  **Create the Managed Instance Group:**
    ```bash
    gcloud compute instance-groups managed create laravel-instance-group \
        --template=laravel-template \
        --size=2 \
        --zone=us-west1-a
    ```
    *   `--size=2`: We are telling the MIG to always keep 2 identical VMs running.
4.  **Create the Load Balancer:** This is a multi-step process to set up a global external HTTP load balancer.
    ```bash
    # 1. Create the backend service (defines how the LB connects to backends)
    gcloud compute backend-services create laravel-backend-service \
        --health-checks=laravel-health-check \
        --global

    # 2. Add our instance group as the backend for this service
    gcloud compute backend-services add-backend laravel-backend-service \
        --instance-group=laravel-instance-group \
        --instance-group-zone=us-west1-a \
        --global

    # 3. Create the URL map (directs incoming URLs to our backend service)
    gcloud compute url-maps create laravel-lb-url-map \
        --default-service laravel-backend-service

    # 4. Create the proxy that uses the URL map
    gcloud compute target-http-proxies create laravel-http-proxy \
        --url-map=laravel-lb-url-map

    # 5. Create the forwarding rule (the public IP address that listens for traffic)
    gcloud compute forwarding-rules create laravel-forwarding-rule \
        --address-name=laravel-lb-ip \
        --global \
        --target-http-proxy=laravel-http-proxy \
        --ports=80
    ```
    *Note: You would need to add an SSL certificate and create an HTTPS proxy for production.*

5.  **Enable Autoscaling:** Tell the instance group to automatically add/remove servers based on load.
    ```bash
    gcloud compute instance-groups managed set-autoscaling laravel-instance-group \
        --max-num-replicas=10 \
        --target-cpu-utilization=0.75 \
        --cool-down-period=90
    ```
    *   `--max-num-replicas=10`: Don't scale beyond 10 VMs.
    *   `--target-cpu-utilization=0.75`: Try to keep the average CPU usage across all VMs at 75%. If it goes higher, add more VMs. If it goes lower, remove them.

You now have a fully scalable, resilient, load-balanced application architecture.

---
**Next Up:** In the final part, we'll explore even more advanced GCP services and provide a roadmap for continued learning.

[**> Proceed to Part 8**](part_8_advanced_topics_and_further_learning.md)

[<< Back to Google Cloud Index](deploying_a_secure_and_scalable_laravel_app_on_google_clouds_index.md)

[<<< Back to Knowledge Base Index](/README.md)
