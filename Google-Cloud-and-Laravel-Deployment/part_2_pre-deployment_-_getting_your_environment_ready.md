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

Now you need to connect the `gcloud` tool to your Google Cloud account and project. This is done with the `init` command.

```bash
gcloud init
```
This command will walk you through a few steps:

1.  **Log in:** It will open a browser window, asking you to log in to the Google account you used to sign up for GCP.
2.  **Select a Project:** After logging in, it will list the GCP projects associated with your account. You will see the `laravel-guide-app` project you created in Part 1. Select it.
3.  **Set a Default Region and Zone:** This is an important concept.
    *   **Region:** A specific geographical location, like `us-west1` (The Dalles, Oregon) or `europe-west2` (London).
    *   **Zone:** An isolated location within a region, like `us-west1-a` or `us-west1-b`.
    *   **Why does this matter for the free tier?** The `e2-micro` VM is only free in certain US regions (`us-west1`, `us-central1`, `us-east1`). It's best to choose one of these now. Let's use `us-west1-a`. When prompted, enter the number corresponding to `us-west1` for the region, and then `us-west1-a` for the zone.

After this, your `gcloud` CLI is configured. It knows which project to work on by default, saving you from having to specify it in every command.

### Step 3: Prepare Your Laravel Application

If you don't have a Laravel application yet, create a new one:
```bash
composer create-project --prefer-dist laravel/laravel gcp-laravel-app
cd gcp-laravel-app
```
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

**Example `.env.example**:**
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
