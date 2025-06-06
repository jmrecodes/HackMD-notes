# Part 6: Post-Deployment - Monitoring, Logging, and Maintenance

[![hackmd-github-sync-badge](https://hackmd.io/@jmrecodes/H1Z5wheXge/badge)](https://hackmd.io/@jmrecodes/H1Z5wheXge)

Deployment is not the finish line. A running application needs to be watched, understood, and backed up. This is often called "observability." In this part, we'll set up the essential tools to ensure our application runs reliably.

[<< Back to Google Cloud Index](deploying_a_secure_and_scalable_laravel_app_on_google_clouds_index.md)

[<<< Back to Knowledge Base Index](/README.md) | [Go to Part 5 >>](part_5_security_-_a_non-negotiable_checklist.md)

---

### Step 1: Centralized Logging with Cloud Logging

When an error happens, you need to know what, where, and when. Sifting through log files on a server with `grep` doesn't scale. Cloud Logging automatically collects logs from your VM's system services (like Nginx) and provides a powerful, centralized interface to search them.

#### Configuring Laravel to Use Cloud Logging
By default, Laravel writes logs to a file in `storage/logs/laravel.log`. We want these logs to go to our central log viewer instead. The easiest way is to configure Laravel to output its logs to the standard error stream (`stderr`), which the Google Cloud Ops Agent on the VM automatically captures.

1.  **(In your VM SSH session)**, edit your Laravel logging configuration file:
    ```bash
    sudo nano /var/www/your-laravel-app/config/logging.php
    ```
2.  Find the `channels` array. We will create a new channel configuration for `stderr`. Add the following block inside the `channels` array:
    ```php
    'stderr' => [
        'driver' => 'monolog',
        'handler' => Monolog\Handler\StreamHandler::class,
        'with' => [
            'stream' => 'php://stderr',
        ],
        'formatter' => env('LOG_STDERR_FORMATTER'),
    ],
    ```
3.  Now, find the `default` key within the configuration file and change it to use a stack that includes our new `stderr` channel.
    ```php
    // 'default' => env('LOG_CHANNEL', 'stack'), // This is the original line
    
    'default' => 'stack', // Change it to this

    'channels' => [
        'stack' => [
            'driver' => 'stack',
            'channels' => ['stderr'], // Changed from ['single']
            'ignore_exceptions' => false,
        ],

        // ... the 'stderr' channel you just added, and the other channels
    ]
    ```
4.  Save and exit (`Ctrl+X`, `Y`, `Enter`).

Now, any call to `Log::info('Hello from Laravel!');` or any exception thrown by your application will be sent to `stderr` and will appear in Cloud Logging.

#### Using the Logs Explorer
1.  In the GCP Console, navigate to **Logging > Logs Explorer**.
2.  In the **Query** box, you can start filtering. The easiest way to find all logs from your VM is to filter by resource:
    *   Click in the query box.
    *   Select **Resource > VM Instance**.
    *   Select your `laravel-server` instance.
    *   Click **Run query**.
3.  The logs you see are "structured." You can click on any log entry to see its full payload, including severity, timestamps, and the log message itself. The logs from Laravel will appear with the `jsonPayload` field populated.

### Step 2: Monitoring and Alerting with Cloud Monitoring

How do you know if your `e2-micro` server is keeping up with traffic? Cloud Monitoring collects and graphs performance metrics.

#### Viewing the Default Dashboard
1.  In the GCP Console, go to **Monitoring > Dashboards**.
2.  In the list of "Google Cloud Dashboards", find and click on **VM Instances**.
3.  At the top of the dashboard, there will be a dropdown to select your instance. Choose `laravel-server`.
4.  You can now see graphs for the most important metrics:
    *   **CPU Utilization:** This is the most critical metric for a web server. If it is consistently above 80-90%, your server is overloaded, and users will experience slowdowns.
    *   **Memory Usage:** How much of your 1GB of RAM is being used.
    *   **Disk I/O:** How much data is being read from and written to the server's disk.
    *   **Network Traffic:** How much data your server is sending and receiving.

#### Creating a Performance Alert
You don't want to stare at dashboards all day. Let's create an alert that emails you if your server is overloaded for a sustained period.

1.  In the GCP Console, go to **Monitoring > Alerting**.
2.  At the top, click **+ CREATE POLICY**.
3.  Click the large **SELECT A METRIC** button.
4.  In the "Filter by resource and metric name" box, type `CPU utilization` and select the one under **VM Instance > CPU**. Click **Apply**.
5.  You'll now see a graph of your VM's CPU usage. Scroll down to the **Configure alert trigger** section.
    *   **Alert trigger:** `Any time series violates`
    *   **Threshold position:** `Above threshold`
    *   **Threshold value:** `80` (for 80%)
    This sets the condition to "fire when CPU is over 80%".
6.  Expand the **Advanced Options**.
    *   **Retest window:** `5 minutes`. This defines how long the condition must be true before an alert is fired. This prevents alerts from brief, temporary spikes.
7.  Click **NEXT**.
8.  On the next screen, you configure who gets notified. Under **Notification Channels**, click the dropdown, then **Manage Notification Channels**.
    *   This will open a new page. Scroll down to **Email** and click **ADD NEW**.
    *   Enter your email address and a display name. Click **Save**.
9.  Go back to the alert creation tab. Click the **Notification Channels** dropdown again and select the channel you just created. Click **OK**.
10. Click **NEXT**.
11. Give the alert a descriptive name, like `High CPU on Laravel Server`. Add a note in the documentation box telling you what the alert means and what you should do (e.g., "CPU has been over 80% for 5 minutes. Consider upgrading the instance size or investigating for performance bugs.").
12. Click **CREATE POLICY**.

You now have an automated safety net to warn you about performance problems.

### Step 3: Maintenance and Backups

Data is invaluable. Backups are non-negotiable.

#### For Cloud SQL (Option 2 in Part 4)
If you chose the managed database, you're in great shape.
1.  Go to **Databases > SQL** in the GCP console.
2.  Click on your `laravel-db` instance.
3.  Click **Edit**.
4.  On the left, go to the **Data Protection** section.
5.  Ensure **Enable automated backups** is checked. You can configure the window when backups are taken.
This also enables **Point-in-time recovery**, which lets you restore your database to its exact state from any minute within the last 7 days (by default). This is incredibly powerful.

#### For Self-Managed MySQL (Option 1 in Part 4)
You are responsible for your own backups. The easiest method is to take a snapshot of the entire disk.

1.  **Manual Snapshot:** In the console, go to **Compute Engine > Snapshots**. Click **CREATE SNAPSHOT**, select your `laravel-server`'s disk, and create it. You can restore this snapshot to a new VM if your server ever fails.
2.  **Automated Snapshot Schedule (Recommended):**
    **(Run these commands in your LOCAL terminal)**
    This creates a policy to automatically take a snapshot every day at 10 PM UTC and keep them for 14 days.
    ```bash
    # Create the snapshot schedule/policy
    gcloud compute resource-policies create snapshot-schedule daily-laravel-backup \
        --region=us-west1 \
        --description="Daily snapshot for laravel-server" \
        --max-retention-days=14 \
        --start-time=22:00 \
        --daily-schedule

    # Attach the policy to your server's disk
    gcloud compute disks add-resource-policies laravel-server \
        --resource-policies=daily-laravel-backup \
        --zone=us-west1-a
    ```
    Your server's disk will now be backed up automatically.

---
**Next Up:** In Part 7, we will look to the future and discuss how to scale your application when you outgrow the free tier.

[**> Proceed to Part 7**](part_7_the_future_-_scaling_your_application_beyond_the_free_tier.md)

[<< Back to Google Cloud Index](deploying_a_secure_and_scalable_laravel_app_on_google_clouds_index.md)

[<<< Back to Knowledge Base Index](/README.md)
