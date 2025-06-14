# Part 2: Pre-Deployment - Getting Your Environment Ready

[![hackmd-github-sync-badge](https://hackmd.io/@jmrecodes/BJKlvheXle/badge)](https://hackmd.io/@jmrecodes/BJKlvheXle)

This part of the guide focuses on setting up your local machine and your Laravel application. Proper preparation here makes the actual deployment process much smoother.

[<< Back to Google Cloud Index](deploying_a_secure_and_scalable_laravel_app_on_google_clouds_index.md)

[<<< Back to Knowledge Base Index](/README.md) | [Go to Part 1 >>](part_1_introduction_to_gcp_and_the_free_tier.md)

---

### Step 1: Install the Google Cloud SDK (`gcloud` CLI)

The `gcloud` command-line interface (CLI) is your primary tool for interacting with your GCP resources. You will use it to create servers, manage permissions, connect via SSH, and more. It's essential.

Follow the official, most up-to-date instructions for your operating system:
*   [Installation Guide for Linux](https://cloud.google.com/sdk/docs/install#linux)
*   [Installation Guide for macOS](https://cloud.google.com/sdk/docs/install#mac)
*   [Installation Guide for Windows](https://cloud.google.com/sdk/docs/install#windows)

After following the installation steps, open a **new** terminal window and verify the installation by running:
```bash
gcloud --version
```
This should output the versions of the installed components.

### Step 2: Initialize the `gcloud` CLI

Now that the SDK is installed, you need to connect it to your Google Cloud account. This one-time setup process configures your credentials and sets some useful defaults.

Run the following command in your terminal:
```bash
gcloud init
```
This interactive command will guide you through a few steps:

1.  **Log in:** It will ask you to log in with a Google account. A browser window will open automatically. Be sure to select the same Google account you used to sign up for Google Cloud in Part 1.
2.  **Select Your Cloud Project:** After you've logged in, the CLI will list all the Google Cloud projects associated with your account. You should see the `laravel-guide-app` project you created earlier. Select it from the list by entering the corresponding number.

You may notice that the command finishes without asking you to set a default location. If you see the message below, don't worry—this is expected for new projects.

```
Not setting default zone/region (this feature makes it easier to use
[gcloud compute] by setting an appropriate default value for the
--zone and --region flag).
... make sure the Compute Engine API is enabled for your project...
```
This simply means we need to manually enable the service for creating virtual machines and then set our default location in the next step.

### Step 2.5: Enable API and Set Default Location (Critical for Free Tier!)

Before we can create a server, we must enable the **Compute Engine API**. We also need to tell `gcloud` which geographical location to create our resources in by default. This is the most important step for ensuring your server is free.

1.  **Enable the Compute Engine API:**
    In your terminal, run the following command. This tells your Google Cloud project that you intend to use virtual machines.
    ```bash
    gcloud services enable compute.googleapis.com
    ```

2.  **Set Your Default Region and Zone:**
    The `e2-micro` virtual machine (our server) is **only free** when it's located in one of these three US regions:
    *   `us-west1` (Oregon)
    *   `us-central1` (Iowa)
    *   `us-east1` (South Carolina)

    To stay within the free tier, you **must** choose one of these. We'll use `us-west1` for this guide. Run these two commands to set your default region and a zone within it:
    ```bash
    gcloud config set compute/region us-west1
    gcloud config set compute/zone us-west1-a
    ```

After completing this, your `gcloud` CLI is fully configured for this project.

### Step 3: Prepare Your Laravel Application

#### Prerequisite: Install Composer

To create a Laravel project, you need Composer, which is a package manager for the PHP programming language. If you don't have it installed, it's a quick setup:

*   [Follow the official Composer installation instructions](https://getcomposer.org/doc/00-intro.md)

You can verify it's installed correctly by running `composer --version`.

#### Create a New Laravel Project

If you don't have a Laravel application yet, open your terminal to the directory where you keep your code projects and run:
```bash
composer create-project --prefer-dist laravel/laravel gcp-laravel-app
cd gcp-laravel-app
```
This command downloads the latest version of Laravel and all its dependencies into a new `gcp-laravel-app` folder.

Now, let's prepare it for the cloud.

#### Environment Variables and the `.env` file

This is the single most important concept for deploying any application securely.

*   The `.env` file in your Laravel project's root is designed for your **local development environment only**.
*   It contains machine-specific settings (like `DB_HOST=localhost`) and, crucially, **secrets** (database passwords, API keys).
*   This file should **NEVER, EVER be committed to a Git repository**. If you do, your secrets will be exposed to anyone who can see the repository.

#### Configuring Git and `.gitignore`

Git is non-negotiable for modern development. Initialize a repository if you haven't already:
```bash
git init
git add .
git commit -m "Initial commit"
```
Now, open the `.gitignore` file in your project root. The default Laravel `.gitignore` is excellent and already includes a line to ignore the `.env` file:
```
/vendor
/node_modules
...
.env  <-- This is the crucial line
...
```
This ensures you can't accidentally commit it.

So how do we get our settings to the production server? We will handle that in two ways later in the guide:
1.  We will generate the production `.env` file on the server itself.
2.  We will use **Secret Manager** to securely inject the secrets, which is the best practice.

#### Create an `.env.example` file

Your `.env.example` file *is* committed to Git. It serves as a template for developers (and for our future selves) to know what environment variables the application needs to run.

Make sure your `.env.example` file mirrors the structure of your `.env` file, but with all secret values removed.

**Example `.env.example`:**
```
APP_NAME=Laravel
APP_ENV=local
APP_KEY=
APP_DEBUG=true
APP_URL=http://localhost

LOG_CHANNEL=stack
LOG_LEVEL=debug

DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=laravel
DB_USERNAME=root
DB_PASSWORD=

...etc
```

Now, set up a remote repository on a service like GitHub or GitLab and push your code.

```bash
# Example for GitHub
git remote add origin https://github.com/your-username/your-repo.git
git branch -M main
git push -u origin main
```

---
**Next Up:** In Part 3, we'll get our hands dirty and provision our first server in the cloud, deploying our Laravel application to it.

[**> Proceed to Part 3**](part_3_the_deployment_-_laravel_on_a_compute_engine_vm.md)

[<< Back to Google Cloud Index](deploying_a_secure_and_scalable_laravel_app_on_google_clouds_index.md)

[<<< Back to Knowledge Base Index](/README.md)