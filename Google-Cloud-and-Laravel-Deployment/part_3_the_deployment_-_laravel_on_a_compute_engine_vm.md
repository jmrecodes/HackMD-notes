# Part 3: The Deployment - Laravel on a Compute Engine VM

[![hackmd-github-sync-badge](https://hackmd.io/@jmrecodes/rkmQv3g7ee/badge)](https://hackmd.io/@jmrecodes/rkmQv3g7ee)

This is the most hands-on part of the guide. We will go from having no server to having a running, publicly accessible (but not yet secure) Laravel application. We will break down every command.

[<< Back to Google Cloud Index](deploying_a_secure_and_scalable_laravel_app_on_google_clouds_index.md)

[<<< Back to Knowledge Base Index](/README.md) | [Go to Part 2 >>](part_2_pre-deployment_-_getting_your_environment_ready.md)

---

### Step 1: Create the Compute Engine VM Instance

We will use the `gcloud` command we configured in Part 2 to create our server. This is called "provisioning".

Open your local terminal and run this single, powerful command.

```bash
gcloud compute instances create laravel-server \
    --project=your-gcp-project-id \
    --zone=us-west1-a \
    --machine-type=e2-micro \
    --image-family=debian-11 \
    --image-project=debian-cloud \
    --boot-disk-size=30GB \
    --tags=http-server,https-server
```

Let's break down exactly what you're telling Google to do:

*   `gcloud compute instances create laravel-server`: The main command. You're asking the "compute" service to "create" a new "instance" (a virtual machine) named `laravel-server`.
*   `--project=your-gcp-project-id`: **ACTION REQUIRED:** Replace `your-gcp-project-id` with your actual Project ID from Part 1 (e.g., `laravel-guide-app-123456`). This tells gcloud which project to create the VM in.
*   `--zone=us-west1-a`: We're creating this VM in the `us-west1-a` zone, which we know has the free tier `e2-micro`.
*   `--machine-type=e2-micro`: This is the specific size of the VM. `e2-micro` includes 2 vCPUs (burstable) and 1 GB of RAM. This is our "Always Free" machine.
*   `--image-family=debian-11` & `--image-project=debian-cloud`: This specifies the operating system. We are asking for a minimal, stable server OS: Debian version 11.
*   `--boot-disk-size=30GB`: The "Always Free" tier includes up to 30GB of standard persistent disk storage. We're allocating all of it.
*   `--tags=http-server,https-server`: These are like labels we are attaching to our VM. They don't do anything by themselves, but we will use them in Part 5 to create firewall rules that allow web traffic to any VM with these tags.

After running the command, GCP will provision your VM. It will take a minute or two. When it's done, you will see a table with the VM's name, zone, machine type, and **External IP**. This IP address is how you will access your site on the internet.

### Step 2: Connect to Your VM via SSH

SSH (Secure Shell) is how you get a terminal inside your new cloud server. `gcloud` makes this incredibly easy and secure.

In your local terminal, run:
```bash
gcloud compute ssh laravel-server --zone=us-west1-a
```
The first time you run this, `gcloud` will automatically create an SSH key pair on your local machine and transfer the public key to the VM's metadata. This means you don't have to manage SSH keys manually. It will ask you to set a passphrase for the key; this is an optional layer of security.

After it connects, your terminal prompt will change. It will look something like `your_username@laravel-server:~$`. **You are now inside your server!** All subsequent commands in this guide should be run inside this SSH session, unless specified otherwise.

### Step 3: Install the "LEMP" Stack

Our Laravel app needs a few key pieces of software to run: **L**inux (which we already have), **E**-Nginx (our web server), **M**-MySQL (our database, for now), and **P**-PHP.

First, let's update our server's list of available software packages and upgrade any outdated ones.
```bash
# Updates the list of available packages
sudo apt update

# Upgrades the installed packages to their latest versions
sudo apt upgrade -y
```
*   `sudo`: This command means "Super User Do". It elevates your privileges to run commands as the root user, which is necessary for installing software.
*   `apt`: This is Debian's package manager.
*   `-y`: This automatically answers "yes" to any prompts, which is useful for scripts.

Now, let's install the stack.
```bash
# Install Nginx, cURL, zip, and unzip utilities
sudo apt install -y nginx curl zip unzip

# Install PHP's FastCGI Process Manager and required extensions for Laravel
sudo apt install -y php8.1-fpm php8.1-mysql php8.1-mbstring php8.1-xml php8.1-bcmath php8.1-curl
```
*   `nginx`: A high-performance web server. It will listen for requests from the internet and direct them to our PHP application.
*   `php8.1-fpm`: The PHP interpreter. Nginx doesn't run PHP code by itself; it passes the request to this `fpm` service.
*   The other `php8.1-*` packages are extensions Laravel relies on for database connections (`mysql`), string manipulation (`mbstring`), math (`bcmath`), etc.

Finally, install Composer, the dependency manager for PHP.
```bash
# Download the installer script
curl -sS https://getcomposer.org/installer -o composer-setup.php

# Run the installer, placing the 'composer' executable in a system-wide location
sudo php composer-setup.php --install-dir=/usr/local/bin --filename=composer
```

### Step 4: Deploy Your Application Code

1.  **Install Git:**
    ```bash
    sudo apt install -y git
    ```
2.  **Clone your project:** We'll clone it into `/var/www/`. Replace the URL with your own repository's URL.
    ```bash
    sudo git clone https://github.com/your-username/your-laravel-app.git /var/www/your-laravel-app
    ```
3.  **Navigate into the directory:**
    ```bash
    cd /var/www/your-laravel-app
    ```
4.  **Install dependencies:** Tell Composer to install all the packages listed in `composer.json`, but without development tools like PHPUnit. The `--optimize-autoloader` flag makes class loading faster in production.
    ```bash
    sudo composer install --optimize-autoloader --no-dev
    ```
5.  **Set up the `.env` file:** Copy the example file to create our production environment file.
    ```bash
    sudo cp .env.example .env
    ```
    Now, open it with a text editor like `nano`.
    ```bash
    sudo nano .env
    ```
    Make these critical changes for production:
    *   `APP_ENV=production`
    *   `APP_DEBUG=false`
    *   `APP_URL=http://YOUR_VM_EXTERNAL_IP` (Use the IP from Step 1)
    Save the file (in nano: `Ctrl+X`, then `Y`, then `Enter`).

6.  **Generate the Application Key:** This is a crucial security step for Laravel.
    ```bash
    sudo php artisan key:generate
    ```
7.  **Set Permissions:** The web server (which runs as the `www-data` user) needs permission to write to the `storage` and `bootstrap/cache` directories to create log files, cache views, etc.
    ```bash
    # Change the owner of these directories to the web server user
    sudo chown -R www-data:www-data storage bootstrap/cache

    # Give the owner and group write permissions
    sudo chmod -R 775 storage bootstrap/cache
    ```

### Step 5: Configure Nginx to Serve Your Site

The final step is to tell Nginx where our application lives.

1.  **Create a new Nginx configuration file:**
    ```bash
    sudo nano /etc/nginx/sites-available/your-laravel-app
    ```
2.  **Paste in this configuration.** This is a standard, secure starting point for Laravel. I've added comments to explain each block.

    ```nginx
    server {
        # Listen on port 80 for incoming connections (HTTP)
        listen 80;

        # This should be your domain name, but for now we'll use the IP.
        # Nginx will use this block for any request to this server.
        server_name _;

        # The root directory of our application. Nginx looks for files here.
        # Note that it points to the 'public' subdirectory.
        root /var/www/your-laravel-app/public;

        # Security headers to help prevent clickjacking and XSS attacks.
        add_header X-Frame-Options "SAMEORIGIN";
        add_header X-XSS-Protection "1; mode=block";
        add_header X-Content-Type-Options "nosniff";

        # The default file to look for if a directory is requested.
        index index.php;

        charset utf-8;

        # This is the main request handling block.
        # It will try to serve a file if it exists ($uri),
        # then a directory ($uri/), and if not found,
        # it passes the request to Laravel's front controller (index.php).
        location / {
            try_files $uri $uri/ /index.php?$query_string;
        }

        # Don't log requests for these common files.
        location = /favicon.ico { access_log off; log_not_found off; }
        location = /robots.txt  { access_log off; log_not_found off; }

        # If a 404 error occurs, pass it to Laravel to handle.
        error_page 404 /index.php;

        # This is the crucial block that handles PHP files.
        location ~ \.php$ {
            # This passes the request to the PHP-FPM service we installed.
            # The 'sock' file is a communication channel between Nginx and PHP.
            fastcgi_pass unix:/var/run/php/php8.1-fpm.sock;
            # This tells PHP which file to execute.
            fastcgi_param SCRIPT_FILENAME $realpath_root$fastcgi_script_name;
            # Includes other standard FastCGI parameters.
            include fastcgi_params;
        }

        # A security measure to block access to hidden files like .env or .git
        location ~ /\.(?!well-known).* {
            deny all;
        }
    }
    ```
    **Remember to replace** `your-laravel-app` with your project's directory name. Save and exit (`Ctrl+X`, `Y`, `Enter`).

3.  **Enable the site:** Nginx loads configs from `sites-enabled`. We create a symbolic link from `sites-available` to `sites-enabled`.
    ```bash
    sudo ln -s /etc/nginx/sites-available/your-laravel-app /etc/nginx/sites-enabled/
    ```
4.  **Remove the default site:** To avoid conflicts, remove the default Nginx welcome page.
    ```bash
    sudo rm /etc/nginx/sites-enabled/default
    ```
5.  **Test and Restart Nginx:**
    ```bash
    # This command checks your config files for syntax errors.
    sudo nginx -t

    # If the test is successful, restart the Nginx service to apply the new config.
    sudo systemctl restart nginx
    ```

**Milestone!** Open your web browser and navigate to `http://YOUR_VM_EXTERNAL_IP`. You should now see the default Laravel landing page!

It's not secure (no HTTPS), and it's not connected to a database, but you have successfully deployed your code to a server you built in the cloud.

---
**Next Up:** In Part 4, we will connect our application to a database, exploring both the free, self-managed option and the more robust, scalable Cloud SQL option.

[**> Proceed to Part 4**](part_4_database_setup_-_managed_vs._self-managed.md)

[<< Back to Google Cloud Index](deploying_a_secure_and_scalable_laravel_app_on_google_clouds_index.md)

[<<< Back to Knowledge Base Index](/README.md)
