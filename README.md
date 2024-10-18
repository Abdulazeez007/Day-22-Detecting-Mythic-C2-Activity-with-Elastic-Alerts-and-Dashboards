# Day-22-Detecting-Mythic-C2-Activity-with-Elastic-Alerts-and-Dashboards

In today’s task, we’ll be diving into how to detect Mythic C2 activity in Elastic by setting up custom alerts and creating a powerful dashboard to visualize potential threats. Specifically, we’ll be focusing on the activity we generated yesterday and how to monitor it using Elastic’s robust detection and visualization tools.

Let’s jump right in!

## Step 1: Creating an Alert for Mythic C2 Activity

The first thing we need is an alert that flags Mythic C2 activity based on our generated logs. Make sure you’re logged into your Elastic web GUI, and follow these steps:

### Navigate to Discover

1. Click on the hamburger icon (menu) in the top left corner and select “Discover” from the list.
2. Refresh all fields to get updated logs.
3. In the search bar, type your generated process name: `svchost-aurorarocks.exe` (or your specific file name). This will retrieve logs related to that process.

### Filter Logs by Event Code 1

1. Once you select the `svchost-aurorarocks.exe` log, you’ll notice Event Code 11.
2. We want to focus on Event Code 1 (which logs process creation). Process creation events are critical, as they can contain important indicators like the MD5 hash of the executable.
3. Update your query:
   
```bash
svchost-aurorarocks.exe and event.code: 1
```

### Investigating Hashes

1. Expand the log entry. Scroll down until you find `winlog.event_data.Hashes`. Here, you’ll see SHA1, MD5, SHA256, and IMPHASH values.
2. Copy the SHA1 hash and run it through an open-source intelligence tool like VirusTotal to check for any known threats. In this case, you might not find anything since it’s a newly generated agent — remember, attackers often change hashes to evade detection.

## Step 2: Setting Up a Detection Rule

Now, let’s create a detection rule to flag any future process creations tied to this activity.

### Custom Query

Our goal is to trigger an alert whenever this specific file (in our case, `Apollo.exe`) is executed. Use the following query to capture both the hash and the original file name:

```bash
svchost-aurorarocks.exe and event.code: 1 and (winlog.event_data.Hashes: A1F75C696553C3E1F887BD0D8B312CDA6D4D57BA8DD587AB59DF2A694A3642AB or winlog.event_data.OriginalFileName: Apollo.exe)
```


### Creating the Rule

1. Head to the hamburger icon, select Security, then choose Rules.
2. Create a new rule with a custom query, paste in the query we saved, and choose the relevant fields:
   - `winlog.event_data.User`
   - `winlog.event_data.ParentImage`
   - `winlog.event_data.ParentCommandLine`
   - `winlog.event_data.Image`
   - `winlog.event_data.CommandLine`
   - `winlog.event_data.CurrentDirectory`
3. Name your rule something meaningful, like `aurora-Mythic-C2-Apollo-Agent-Detected`.
4. Set the rule to run every 5 minutes.
5. For severity, set it to Critical.
6. Click **Create & Enable Rule** to finalize it.

## Step 3: Building the Mythic C2 Dashboard

With alerts set up, it’s time to create a dashboard that gives us an at-a-glance view of suspicious activities. Dashboards in Elastic are great for visualizing event data in real-time.

### Navigate to the Dashboard Section

1. Click on the hamburger icon and select **Dashboard** under the Analytics tab.
2. Create a new dashboard by selecting **New Visualization**.

### Event ID 1: Process Creation (Powershell, cmd, rundll32)

We want to track process creation events related to potentially malicious processes.

#### Query

```bash
event.code: 1 and event.provider: Microsoft-Windows-Sysmon and (powershell or cmd or rundll32)
```

#### Fields to visualize:
- `winlog.event_data.user`
- `winlog.event_data.ParentImage`
- `winlog.event_data.ParentCommandLine`
- `winlog.event_data.Image`
- `winlog.event_data.CommandLine`
- `winlog.event_data.CurrentDirectory`

### Event ID 3: Network Connections (Outbound)

Track any outbound network connections initiated by a process.

#### Query

```bash
event.code: 3 and event.provider: Microsoft-Windows-Sysmon and winlog.event_data.Initiated: true
```

#### Fields to visualize:
- `winlog.event_data.Image`
- `winlog.event_data.SourceIp`
- `winlog.event_data.DestinationIp`
- `winlog.event_data.DestinationPort`

### Event ID 5001: Windows Defender Disabled

Capture events where Windows Defender was disabled — a major red flag in any environment.

#### Query

```bash
event.code: 5001 and event.provider: Microsoft-Windows-Windows Defender
```

#### Fields to visualize:
- `host.hostname`
- `winlog.event_data.ProductName`
- `Event.code`

## Step 4: Wrapping It Up

Once you’ve added the necessary panels to your dashboard, save it for future monitoring. This dashboard will give you a bird’s-eye view of potential threats, showing critical events like process creation, network connections, and security software tampering.

By following this guide, you’ve set up both alerts and visualizations that can help detect and respond to Mythic C2 activity in real-time. Stay tuned for the next blog, where we’ll explore setting up a free ticketing system to manage cases and alerts effectively.



