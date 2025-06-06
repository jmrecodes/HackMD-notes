# Part 1: Introduction to GCP & The Free Tier

[![hackmd-github-sync-badge](https://hackmd.io/5wgPxx1OTfaVDYUVeofRpQ/badge)](https://hackmd.io/5wgPxx1OTfaVDYUVeofRpQ)

Welcome to the first part of the guide! The goal here is to get you started with Google Cloud Platform (GCP) safely and with a clear understanding of the free offerings, so you can learn and experiment without cost anxiety.

[<< Back to Google Cloud Index](deploying_a_secure_and_scalable_laravel_app_on_google_clouds_index.md)

[<<< Back to Knowledge Base Index](/README.md)

---

### Understanding the "Free" in Google Cloud

This is the most crucial part for any beginner. Google Cloud has two programs that give you free access to resources. Understanding the difference is key to managing costs.

#### Program 1: The 90-day, $300 Free Trial

*   **What it is:** When you first sign up for GCP, you are typically enrolled in a free trial. Google gives you $300 worth of credit that you can use on almost any GCP service.
*   **Duration:** This trial lasts for 90 days or until you spend the $300, whichever comes first.
*   **Purpose:** This is designed for evaluation. It lets you experiment with powerful, paid services (like large virtual machines, or advanced AI APIs) to see if they fit your needs, without any financial commitment.
*   **Important:** Usage of *any* service, even those that have an "Always Free" tier, will consume your $300 credit during the trial period. After the trial ends, you are moved to a standard pay-as-you-go account.

#### Program 2: The "Always Free" Tier

*   **What it is:** This is a collection of specific products that have a monthly usage allowance that you can use for free, *forever*. This is separate from the Free Trial.
*   **Duration:** It doesn't expire. As long as you stay within the monthly limits, you will not be charged.
*   **Purpose:** This is designed to let you run small applications, like the one we are building, indefinitely at no cost.
*   **Reference:** You should bookmark the official [Google Cloud Free Tier documentation](https://cloud.google.com/free/docs/free-cloud-features). This is the source of truth for all limits.

**Our Focus:** This guide will focus *exclusively* on using services within the **Always Free** tier limits to ensure your application can run for free long after the 90-day trial is over.

### The Building Blocks of Our Free Laravel Stack

Based on the official documentation, here are the free resources we will use:

| Service | Free Tier Allowance | What We'll Use It For |
| :--- | :--- | :--- |
| **Compute Engine** | 1 `e2-micro` VM per month | Our primary web server to run Nginx, PHP, and our Laravel code. |
| **Cloud Storage** | 5 GB-months of Standard storage | Later in the guide, we'll use this for storing user-uploaded files. |
| **Secret Manager** | 6 active secret versions, 10,000 access ops/month | Securely storing our database password, API keys, and other secrets. |
| **Cloud Logging/Monitoring**| Generous free ingestion and storage allowances | Viewing our application logs and monitoring our server's performance. |
| **Cloud DNS** | Free public zones | (Optional) Managing our domain's DNS records if you choose to. |


### Step 1: Creating Your Google Cloud Account

1.  Navigate to the [Google Cloud Console](https://console.cloud.google.com/).
2.  You will be prompted to sign in with a Google Account.
3.  You'll be guided through a sign-up process. You **must** provide a credit card or other valid payment method.
    *   **Is this safe?** Yes. This is for identity verification and to protect Google from fraudulent usage.
    *   **Will I be charged?** You will **NOT** be charged automatically. Your account starts in the free trial mode. To be charged, you would have to manually upgrade your billing account to a paid account *and* exceed the free tier limits. We will add a safety net in Step 3.

### Step 2: Understanding and Creating a Project

In GCP, everything happens inside a **Project**.

*   **What it is:** A project is a container for all your cloud resources (VMs, databases, secrets, etc.). It's also the boundary for billing, permissions, and APIs. Think of it as a dedicated workspace for one application. You can have many projects under your account.
*   **Creating your first project:**
    1.  After signing up, you will likely be in a default project called "My First Project". It's better to create a new one.
    2.  In the top bar of the Google Cloud Console, click on the project name dropdown.
    3.  In the dialog that appears, click **"New Project"**.
    4.  Give your project a descriptive name, like `laravel-guide-app`. Note the **Project ID** below the name. This is a unique, unchangeable identifier for your project. You will use it often.
    5.  Click **Create**. It might take a minute for the project to be ready. Ensure it's selected in the top bar.

### Step 3: CRUCIAL - Setting a Budget Alert

This is your safety net. We will create a budget that alerts you if costs ever start to accrue, giving you plenty of time to react.

1.  In the navigation menu (the "hamburger" icon ☰), go to **Billing**.
2.  In the Billing navigation menu on the left, select **Budgets & alerts**.
3.  Click **+ CREATE BUDGET**.
4.  **Scope:** Leave the scope as is; it should apply to your entire billing account.
5.  **Amount:**
    *   Select **Budget type: Specified amount**.
    *   **Target amount:** Enter a very low number. **`$1.00`** is perfect.
6.  **Actions:** This is where you set the alerts. Under **"Manage notifications"**, by default, it will alert you at 50%, 90%, and 100% of the budgeted amount. This is good.
    *   **Crucially, check the box for "Email alerts to billing administrators and users"**.
7.  Click **Finish**.

You're all set! Now, if your monthly usage ever projects to exceed $1, you will get an email. This allows you to experiment with total peace of mind.

---
**Next Up:** In Part 2, we will prepare our local development environment by installing the necessary tools and getting our Laravel application ready for the cloud.

[**> Proceed to Part 2**](part_2_pre-deployment_-_getting_your_environment_ready.md)

[<< Back to Google Cloud Index](deploying_a_secure_and_scalable_laravel_app_on_google_clouds_index.md)

[<<< Back to Knowledge Base Index](/README.md)
