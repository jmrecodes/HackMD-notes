# Part 5: Security - A Non-Negotiable Checklist

[![hackmd-github-sync-badge](https://hackmd.io/@jmrecodes/SJavwhlmgl/badge)](https://hackmd.io/@jmrecodes/SJavwhlmgl)

An unsecured application is a major liability. In this part, we will lock down our application by implementing three layers of security: at the network level (firewall), at the transport level (SSL/HTTPS), and at the application level (secrets management).

[<< Back to Google Cloud Index](deploying_a_secure_and_scalable_laravel_app_on_google_clouds_index.md)

[<<< Back to Knowledge Base Index](/README.md) | [Go to Part 4 >>](part_4_database_setup_-_managed_vs._self-managed.md)

---

### Step 1: Network Firewall

By default, Google Cloud blocks all incoming traffic to your VM from the internet, except for a few essential ports like SSH. This is a good security posture. We need to explicitly allow traffic for our web server.

Remember in Part 3 when we created the VM with `--tags=http-server,https-server`? We will now use those tags.

**(Run these commands in your LOCAL terminal)**

```bash
# Allow HTTP traffic on TCP port 80 from any source
gcloud compute firewall-rules create allow-http \
    --allow tcp:80 \
    --source-ranges 0.0.0.0/0 \
    --target-tags http-server \
    --description="Allow standard HTTP traffic"

# Allow HTTPS traffic on TCP port 443 from any source
gcloud compute firewall-rules create allow-https \
    --allow tcp:443 \
    --source-ranges 0.0.0.0/0 \
    --target-tags https-server \
    --description="Allow standard HTTPS traffic"
```
*   `--allow tcp:80`: This opens the standard port for unencrypted web traffic.
*   `--allow tcp:443`: This opens the standard port for encrypted HTTPS traffic.
*   `--source-ranges 0.0.0.0/0`: This CIDR block means "any IP address from anywhere on the internet".
*   `--target-tags http-server`: This rule applies to any VM that has the `http-server` tag. This is powerful because you can apply the same rule to many VMs just by giving them the tag.

Your server can now receive web traffic, but it's still unencrypted.

### Step 2: Add a Domain and Free SSL (HTTPS)

Running a production website over plain HTTP is not acceptable. It's insecure for your users and bad for SEO. We'll use Let's Encrypt, a free certificate authority, to secure our site.

#### Prerequisites
You need a domain name (e.g., `my-cool-laravel-app.com`).

1.  **Point Your Domain:** Go to your domain name registrar (where you bought the domain) and find the DNS management page. You need to create an **`A` record**.
    *   **Host/Name:** `@` (which means the root domain)
    *   **Value/Points to:** Your VM's external IP address.
    *   **TTL (Time To Live):** Can be left at the default.
    It can take anywhere from a few minutes to a few hours for DNS changes to propagate across the internet.

2.  **Install Certbot:** Certbot is a tool that automates fetching and renewing Let's Encrypt certificates.
    **(Run these commands in your VM SSH session)**
    ```bash
    sudo apt install -y certbot python3-certbot-nginx
    ```
    This installs Certbot itself and the Nginx plugin, which allows Certbot to automatically reconfigure your web server for you.

3.  **Run Certbot:**
    **ACTION REQUIRED:** Replace `your-domain.com` with your actual domain name.
    ```bash
    sudo certbot --nginx -d your-domain.com -d www.your-domain.com --non-interactive --agree-tos -m your-email@example.com
    ```
    *   `--nginx`: Use the Nginx plugin.
    *   `-d your-domain.com -d www.your-domain.com`: Request a certificate that covers both the root domain and the `www` subdomain.
    *   `--non-interactive --agree-tos`: Automate the process by agreeing to the terms of service.
    *   `-m your-email@example.com`: **ACTION REQUIRED:** Provide your email address for urgent renewal notices.

    Certbot will communicate with Let's Encrypt to verify you own the domain. Then, it will ask if you want to redirect HTTP traffic to HTTPS. You should choose the redirect option. It will modify your Nginx config file in `/etc/nginx/sites-available/` to add the SSL certificate paths and the redirect rule.

    Visit `https://your-domain.com`. You should see the green lock icon! Certbot also sets up a cron job to automatically renew your certificate before it expires.

### Step 3: Production-Grade Secrets Management

Storing secrets (like your database password) in a plain-text `.env` file on the server is a security risk. If an attacker ever gains access to your server's files, they have all your keys. The professional way to handle this is with a dedicated secret vault. We will use GCP's **Secret Manager**, which has a generous free tier.

**The Strategy:**
1.  Create a dedicated, low-privilege **Service Account** for our app.
2.  Create a secret in Secret Manager containing the *entire contents* of our production `.env` file.
3.  Grant our new Service Account permission to access *only that specific secret*.
4.  Configure our VM to run as this new service account.
5.  Create a startup script that fetches the secret and writes it to the `.env` file when the VM boots up.

#### Step 3.1: Create a Dedicated Service Account (Local Terminal)
```bash
gcloud iam service-accounts create laravel-app-sa \
    --display-name="Laravel App Service Account"
```
This creates an "identity" for our application with no permissions yet.

#### Step 3.2: Create the Secret (Local Terminal)
1.  **Enable the API:**
    ```bash
    gcloud services enable secretmanager.googleapis.com
    ```
2.  **Create a local `prod.env` file:** On your local machine, create a file named `prod.env`. Copy the contents of the `.env` file from your server into it. Make sure it has your final production database credentials, a valid `APP_KEY`, `APP_ENV=production`, etc.
3.  **Create the secret container:**
    ```bash
    gcloud secrets create laravel-prod-env --replication-policy="automatic"
    ```
4.  **Add the file content as the first version of the secret:**
    ```bash
    gcloud secrets versions add laravel-prod-env --data-file="prod.env"
    ```
5.  **Securely delete the local file:**
    ```bash
    rm prod.env
    ```
Your secrets are now stored securely in Google's infrastructure.

#### Step 3.3: Grant Permissions (Local Terminal)
1.  Get the full email of your new service account:
    ```bash
    gcloud iam service-accounts list --filter="displayName='Laravel App Service Account'" --format='value(email)'
    ```
    It will look like `laravel-app-sa@your-project-id.iam.gserviceaccount.com`. Copy it.
2.  Bind the permission: This command gives your service account the `secretAccessor` role, but *only* for the `laravel-prod-env` secret. This is the **Principle of Least Privilege** in action.
    ```bash
    gcloud secrets add-iam-policy-binding laravel-prod-env \
        --member="serviceAccount:THE_SERVICE_ACCOUNT_EMAIL_YOU_COPIED" \
        --role="roles/secretmanager.secretAccessor"
    ```

#### Step 3.4: Reconfigure the VM (Local Terminal)
We need to tell our VM to "act as" this new service account.
1.  **Stop the VM:**
    ```bash
    gcloud compute instances stop laravel-server --zone=us-west1-a
    ```
2.  **Attach the new service account:**
    ```bash
    gcloud compute instances set-service-account laravel-server \
        --zone=us-west1-a \
        --service-account=THE_SERVICE_ACCOUNT_EMAIL_YOU_COPIED
    ```
3.  **Create a startup script:** On your **local machine**, create a file called `startup-script.sh`. This script will run automatically every time the VM boots.
    ```bash
    #!/bin/bash
    # Pull the secret from Secret Manager and decode it
    gcloud secrets versions access latest --secret="laravel-prod-env" --format='get(payload.data)' | base64 --decode > /var/www/your-laravel-app/.env
    # Set the correct permissions on the file
    chown www-data:www-data /var/www/your-laravel-app/.env
    chmod 664 /var/www/your-laravel-app/.env
    ```
4.  **Add the startup script to the VM's metadata:**
    ```bash
    gcloud compute instances add-metadata laravel-server \
        --zone=us-west1-a \
        --metadata-from-file startup-script=startup-script.sh
    ```
5.  **Start the VM:**
    ```bash
    gcloud compute instances start laravel-server --zone=us-west1-a
    ```

Now, when the VM boots, it automatically and securely pulls its configuration from Secret Manager. You no longer need to manually manage the `.env` file on the server. To update a secret, you just add a new version in Secret Manager and restart the VM.

---
**Next Up:** In Part 6, we'll learn how to keep an eye on our now-deployed and secured application using cloud-native logging and monitoring tools.

[**> Proceed to Part 6**](part_6_post-deployment_-_monitoring_logging_and_maintenance.md)

[<< Back to Google Cloud Index](deploying_a_secure_and_scalable_laravel_app_on_google_clouds_index.md)

[<<< Back to Knowledge Base Index](/README.md)
