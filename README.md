# Day-22-Detecting-Mythic-C2-Activity-with-Elastic-Alerts-and-Dashboards

# Detecting Mythic C2 Activity: Creating Alerts and Dashboards in Elastic

In today’s task, we’ll be diving into how to detect Mythic C2 activity in Elastic by setting up custom alerts and creating a powerful dashboard to visualize potential threats. Specifically, we’ll be focusing on the activity we generated yesterday and how to monitor it using Elastic’s robust detection and visualization tools.

Let’s jump right in!

## Step 1: Creating an Alert for Mythic C2 Activity

The first thing we need is an alert that flags Mythic C2 activity based on our generated logs. Make sure you’re logged into your Elastic web GUI, and follow these steps:

### Navigate to Discover

1. Click on the hamburger icon (menu) in the top left corner and select “Discover” from the list.
2. Refresh all fields to get updated logs.
3. In the search bar, type your generated process name: `svchost-pheonixrocks.exe` (or your specific file name). This will retrieve logs related to that process.

### Filter Logs by Event Code 1

1. Once you select the `svchost-pheonixrocks.exe` log, you’ll notice Event Code 11.
2. We want to focus on Event Code 1 (which logs process creation). Process creation events are critical, as they can contain important indicators like the MD5 hash of the executable.
3. Update your query:
   
```bash
svchost-pheonixrocks.exe and event.code: 1
```
