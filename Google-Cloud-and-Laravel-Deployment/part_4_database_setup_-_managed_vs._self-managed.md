# Part 4: Database Setup - Managed vs. Self-Managed

[![hackmd-github-sync-badge](https://hackmd.io/@jmrecodes/B1PrDngmgg/badge)](https://hackmd.io/@jmrecodes/B1PrDngmgg)

A Laravel application isn't complete without a database. In this section, we'll connect our app to a MySQL database. This is the first major architectural decision you'll make, with significant trade-offs in cost, scalability, and maintenance effort. Read both options carefully before choosing.

[<< Back to Google Cloud Index](deploying_a_secure_and_scalable_laravel_app_on_google_clouds_index.md)

[<<< Back to Knowledge Base Index](/README.md) | [Go to Part 3 >>](part_3_the_deployment_-_laravel_on_a_compute_engine_vm.md)

---

### Understanding the Trade-Off: Cost vs. Convenience

| Feature | Option 1: Self-Managed on VM | Option 2: Managed with Cloud SQL |
| :--- | :--- | :--- |
| **Cost** | **$0.00 (Always Free)** | **Not Free.** Uses Free Trial credit, then small monthly cost. |
| **Maintenance**| **High.** You do everything: installs, updates, security, backups. | **Low.** Google handles all infrastructure, patching, and backups. |
| **Performance** | **Lower.** Competes for CPU/RAM with your web server. | **Higher.** Dedicated, optimized instance for database workloads. |
| **Scalability** | **Very Difficult.** Requires migrating to a bigger server. | **Easy.** Upgrade size or add replicas with a few clicks. |
| **Reliability** | **Lower.** If the VM goes down, your app and database go down. | **Higher.** Managed service with uptime guarantees. |
| **Backups** | **Manual.** You must set up and manage your own backup process. | **Automated.** Point-in-time recovery and daily backups are built-in. |

**Recommendation for Beginners:** Start with **Option 1**. It's free and a great way to learn the fundamentals of database administration.
**Recommendation for Production:** For any serious application, **Option 2** is strongly recommended as soon as your budget allows. The time you save on maintenance is worth far more than the small monthly cost.

---

### Option 1: Self-Managed MySQL on Compute Engine (The Free Way)

Here, we install the MySQL server directly onto our `laravel-server` VM.

**(All commands are to be run inside your `gcloud compute ssh` session)**

#### Step 1: Install MySQL Server
```bash
sudo apt update
sudo apt install -y mysql-server
```
This installs the MySQL database software.

#### Step 2: Secure the Installation
MySQL comes with a script to lock down default settings.
```bash
sudo mysql_secure_installation
```
This script will ask you a series of questions. It is **highly recommended** to answer 'Y' (Yes) to all of them for a production environment.
*   **VALIDATE PASSWORD COMPONENT:** Say Yes. This enforces strong passwords. Choose a level (0, 1, or 2). Level 1 is a good choice.
*   **Remove anonymous users?** Yes.
*   **Disallow root login remotely?** Yes. This is crucial.
*   **Remove test database and access to it?** Yes.
*   **Reload privilege tables now?** Yes.

#### Step 3: Create a Database and Application User
It's bad practice for your application to connect to the database as the `root` user. We will create a dedicated user for Laravel.

1.  Log into the MySQL command line as the root user:
    ```bash
    sudo mysql
    ```
    Your prompt will change to `mysql>`.
2.  Run the following SQL commands. **ACTION REQUIRED:** Replace `'a-very-strong-password'` with a unique, strong password. Save this password somewhere safe.
    ```sql
    -- Create an empty database for our application
    CREATE DATABASE laravel;

    -- Create a new user named 'laraveluser' that can only connect from 'localhost' (the same machine)
    CREATE USER 'laraveluser'@'localhost' IDENTIFIED BY 'a-very-strong-password';

    -- Grant this new user all permissions on ONLY the 'laravel' database
    GRANT ALL PRIVILEGES ON laravel.* TO 'laraveluser'@'localhost';

    -- Apply the changes
    FLUSH PRIVILEGES;

    -- Exit the MySQL prompt
    EXIT;
    ```
#### Step 4: Configure Laravel and Run Migrations
1.  Edit the `.env` file on your server:
    ```bash
    sudo nano /var/www/your-laravel-app/.env
    ```
2.  Update the `DB_` variables with the credentials you just created:
    ```
    DB_CONNECTION=mysql
    DB_HOST=127.0.0.1  // or 'localhost'
    DB_PORT=3306
    DB_DATABASE=laravel
    DB_USERNAME=laraveluser
    DB_PASSWORD=a-very-strong-password
    ```
3.  Save the file (`Ctrl+X`, `Y`, `Enter`).
4.  Test the connection by running Laravel's database migrations:
    ```bash
    cd /var/www/your-laravel-app
    sudo php artisan migrate
    ```
    You will be asked to confirm running migrations in production. Type `yes`. If it completes successfully, your application is connected!

---

### Option 2: Managed Database with Cloud SQL (The Production Way)

This path uses your Free Trial credits. We will provision a new, managed MySQL instance and connect our VM to it securely.

**(Run these commands in your LOCAL terminal, not the SSH session, unless specified)**

#### Step 1: Enable the Required API
```bash
gcloud services enable sqladmin.googleapis.com
```

#### Step 2: Create the Cloud SQL Instance
This command provisions a new MySQL 8.0 database instance. The `--tier` specifies the machine size. `db-n1-standard-1` is a small but capable size.
```bash
gcloud sql instances create laravel-db \
    --database-version=MYSQL_8_0 \
    --tier=db-n1-standard-1 \
    --region=us-west1
```
This step will take several minutes.

#### Step 3: Create the User and Database
**ACTION REQUIRED:** Replace `'a-very-strong-password'` with a secure password.
```bash
# Create the user
gcloud sql users create laraveluser --host=% --instance=laravel-db --password='a-very-strong-password'

# Create the database
gcloud sql databases create laravel --instance=laravel-db
```
*   `--host=%`: This allows the user to connect from any IP address, which is required because the connection will be coming through the secure proxy.

#### Step 4: Install and Configure the Cloud SQL Auth Proxy
This is the most secure way to connect a VM to a Cloud SQL instance. The proxy handles authentication with IAM and encrypts all traffic. It avoids the need to open your database to the public internet.

**(Now, switch to your SSH session on the `laravel-server` VM)**

1.  **Download the proxy:**
    ```bash
    curl -o cloud-sql-proxy https://storage.googleapis.com/cloud-sql-proxy/v2.10.0/cloud-sql-proxy.linux.amd64
    chmod +x cloud-sql-proxy
    sudo mv cloud-sql-proxy /usr/local/bin/
    ```
2.  **Get the Instance Connection Name:** You need this unique ID to tell the proxy which database to connect to. Run this command on your **local** machine:
    ```bash
    gcloud sql instances describe laravel-db --format='value(connectionName)'
    ```
    Copy the output. It will look like `your-project-id:us-west1:laravel-db`.
3.  **Create a `systemd` service for the proxy:** This will ensure the proxy runs automatically and restarts if it fails.
    ```bash
    sudo nano /etc/systemd/system/cloud-sql-proxy.service
    ```
    Paste the following into the file. **ACTION REQUIRED:** Replace `<INSTANCE_CONNECTION_NAME>` with the value you just copied.
    ```ini
    [Unit]
    Description=Google Cloud SQL Auth Proxy
    After=network.target

    [Service]
    User=root
    Group=root
    ExecStart=/usr/local/bin/cloud-sql-proxy --address 0.0.0.0 --port 3306 <INSTANCE_CONNECTION_NAME>
    Restart=always
    StandardOutput=journal

    [Install]
    WantedBy=multi-user.target
    ```
4.  **Enable and start the service:**
    ```bash
    # Reload systemd to recognize the new service file
    sudo systemctl daemon-reload

    # Enable the proxy to start on boot
    sudo systemctl enable cloud-sql-proxy.service

    # Start the proxy now
    sudo systemctl start cloud-sql-proxy.service

    # Check its status
    sudo systemctl status cloud-sql-proxy.service
    ```
    You should see a green "active (running)" status.

#### Step 5: Configure Laravel and Run Migrations
1.  **(On the VM)** Edit the `.env` file:
    ```bash
    sudo nano /var/www/your-laravel-app/.env
    ```
2.  Update the `DB_` variables. The host is `127.0.0.1` because your app is talking to the local proxy, which then securely forwards traffic to Cloud SQL.
    ```
    DB_CONNECTION=mysql
    DB_HOST=127.0.0.1
    DB_PORT=3306
    DB_DATABASE=laravel
    DB_USERNAME=laraveluser
    DB_PASSWORD=a-very-strong-password
    ```
3.  Save the file, then run migrations:
    ```bash
    cd /var/www/your-laravel-app
    sudo php artisan migrate --force
    ```
    Success! Your Laravel app is now connected to a robust, managed, production-ready database.

---
**Next Up:** In Part 5, we will secure our application by setting up a firewall, adding a free SSL certificate, and moving our secrets into a secure vault.

[**> Proceed to Part 5**](part_5_security_-_a_non-negotiable_checklist.md)

[<< Back to Google Cloud Index](deploying_a_secure_and_scalable_laravel_app_on_google_clouds_index.md)

[<<< Back to Knowledge Base Index](/README.md)
