<h1>Microsoft Azure SIEM Project</h1>

<h2>Description</h2>
In this project, I set up a cloud-based SIEM (Security Information and Event Management) system using Microsoft Azure and Azure Sentinel. I ran a Windows 10 virtual machine on Oracle VirtualBox, which I used to RDP into the virtual machine I created in Azure. This Azure VM was configured as a honeypot, with its firewalls disabled to make it vulnerable to internet attacks.
Using PowerShell, I extracted the IP addresses of attackers from the Windows Event logs of the Azure VM and sent them to an external API to retrieve geographic data such as country, state, and city. This data was ingested into Azure's Log Analytics Workspace, where I created custom logs that stored the geographic and attack details.
Finally, I used Azure Sentinel to map the attacker data, visualizing the locations of failed RDP login attempts in real-time. This project demonstrates practical experience in SIEM tools and tracking live cyberattacks for monitoring and analysis.
<br />


<h2>Languages and Utilities Used</h2>

- <b>Microsoft Azure Sentinel</b>
- <b>Oracle VirtualBox</b>
- <b>PowerShell</b> 
- <b>Kusto Query Language (KQL)</b>
- <b>Remote Desktop Protocol (RDP)</b> 

<h2>Environments Used </h2>

- <b>Windows 10</b> (21H2)

## Step 1: Create a Microsoft Azure Subscription.
- I started by setting up a Microsoft Azure subscription, taking advantage of the $100 in free credits provided for new accounts. This was necessary to create and manage the resources used throughout the project.

## Step 2: Create and expose a Honeypot Virtual Machine (VM)
 - I created a Windows 10 VM in Azure, naming it "Verycoolstuff". This VM served as the honeypot, which was intentionally exposed to the internet by configuring the inbound rules in its Network Security Group to allow all traffic (any protocol, any port).
    * Resource Group: I placed the VM in a resource group called "Honeypot."
  
<img width="1438" alt="Screen Shot 2024-09-10 at 2 48 29 PM" src="https://github.com/user-attachments/assets/5dc1150b-9e17-45d5-a534-51b2263695d8">

<img width="383" alt="Screen Shot 2024-09-10 at 2 52 52 PM" src="https://github.com/user-attachments/assets/dc2c8e3a-bee5-4cf2-a1ab-194ee0e3f1f7">

> Network Security Group to allow all traffic (any protocol, any port).

## Step 3: Set Up Oracle VirtualBox to RDP into the Azure VM
- To manage the honeypot, I set up Oracle VirtualBox on my local machine to run a Windows 10 virtual machine. From there, I used Remote Desktop Protocol (RDP) to access "Verycoolstuff" (the Azure VM), allowing me to interact with the honeypot directly without needing to log in from another external machine.

<img width="500" alt="Screen Shot 2024-09-10 at 2 48 59 PM" src="https://github.com/user-attachments/assets/e6981534-bf6c-4309-b738-659d818483ac">

> Oracle VirtualBox management screen

## Step 4: Create a Log Analytics Workspace
- Next, I set up a Log Analytics Workspace named "Law-Honeypot1." This workspace was responsible for collecting log data from the honeypot VM, including security event logs and custom logs that I later created for geographic data.

<img width="500" alt="Screen Shot 2024-09-10 at 2 54 59 PM" src="https://github.com/user-attachments/assets/c4c19f3e-3ab0-4333-b390-f10864aa75d8">

> Log Analytics workspace creation screen

<img width="500" alt="Screen Shot 2024-09-10 at 4 34 18 PM" src="https://github.com/user-attachments/assets/d08a14e3-8cd5-4391-940b-36d49c69e1b7">

> VM Event Viewer: Security Event Logs

## Step 5: Configure Microsoft Defender for Cloud
- I enabled Microsoft Defender for Cloud to ensure more comprehensive monitoring of the honeypot VM. I activated the necessary security settings:

  * Turned on Cloud Security Posture Management and Servers.
  
<img width="700" alt="Screen Shot 2024-09-10 at 2 55 57 PM" src="https://github.com/user-attachments/assets/36e52047-4db1-4956-a504-e0bd2ed1b2ed">

  * Configured data collection to gather All Events from the VM.

<img width="550" alt="Screen Shot 2024-09-10 at 4 43 10 PM" src="https://github.com/user-attachments/assets/bf9b8bdf-fa97-46ab-a6ca-4d6ff52fcfcc">

## Step 6: Connect the VM to the Log Analytics Workspace
- After configuring Defender, I connected the VM "Verycoolstuff" to the Log Analytics Workspace "Law-Honeypot1." This connection ensured that logs from the VM were being ingested into the workspace for further analysis and querying.

<img width="600" alt="Screen Shot 2024-09-10 at 2 57 10 PM" src="https://github.com/user-attachments/assets/dd0dfe85-7714-40de-aeff-d50338456611">

> VM Verycoolstuff connected to Law-honeypot1


## Step 7: Set Up Azure Sentinel
- I deployed Azure Sentinel and attached the Log Analytics Workspace "Law-honeypot1" to it. Sentinel acted as the SIEM (Security Information and Event Management) system, enabling me to monitor, query, and visualize the attack data being collected from the honeypot.

<img width="1440" alt="Screen Shot 2024-09-10 at 2 57 48 PM" src="https://github.com/user-attachments/assets/d139857f-5da6-4be8-89b1-ff0e0cce83cc">

> Law-honeypot1 connected to Sentinel


## Step 8: Disable the Firewall on the Honeypot VM
To ensure the honeypot VM was fully exposed, I disabled the Windows Defender firewall on "Verycoolstuff." 
- I logged into the VM using Remote Desktop Protocol (RDP) via the Oracle VirtualBox Windows 10 instance I had set up earlier.

<img width="500" alt="Screen Shot 2024-09-10 at 2 49 31 PM" src="https://github.com/user-attachments/assets/913f957b-aa20-4227-b7fe-5da7a3840112">

- After logging in, I turned off the firewall settings for all profiles (Domain, Private, and Public), making the VM vulnerable to external attacks.

<img width="500" alt="Screen Shot 2024-09-10 at 3 01 26 PM" src="https://github.com/user-attachments/assets/2a69656f-0203-4747-8161-efb949680dd1">

- I then used the ping command from my local machine to confirm that the VM was reachable and the firewall settings had been successfully disabled

<img width="500" alt="Screen Shot 2024-09-10 at 4 57 29 PM" src="https://github.com/user-attachments/assets/a92ec3a7-5988-4889-a701-29cddbeb4dce">

## Step 9: Scripting the Security Log Exporter
I ran a PowerShell script inside "Verycoolstuff" to extract attacker data from the Windows Event Viewer logs. Specifically, the script targeted failed RDP login attempts (event ID 4625).
* I signed up for a free API key from ipgeolocation.io to gather geographic information based on the attackers' IP addresses.

<img width="650" alt="Screen Shot 2024-09-10 at 3 04 50 PM" src="https://github.com/user-attachments/assets/65d12a96-fbc5-42b3-b900-207c9808b9b4">

* The script continuously ran on the VM, exporting failed RDP login data and using the API to retrieve the location information (latitude, longitude, country) for the attackers.

<img width="500" alt="Screen Shot 2024-09-10 at 3 02 41 PM" src="https://github.com/user-attachments/assets/44e2bfdf-b32b-40c6-8e7b-bdcdba7755f3">

> Custom Powershell script parsing data from 3rd party API


## Step 10: Create a Custom Log in Log Analytics Workspace
As part of the process, I had to adapt the way custom logs are created in Azure. I copied some of the output from the PowerShell script into a .txt file and followed these steps:
- Navigated to Log Analytics Workspaces > Law-Honeypot1 > Settings > Tables > Create > New Custom Log (MMA Based).

<img width="1435" alt="Screen Shot 2024-09-10 at 3 38 42 PM" src="https://github.com/user-attachments/assets/7e339ad6-a3dd-4100-a3c5-8cd8b79966a5">

-  Uploaded the sample .txt file, reviewed the record delimiter, set the collection path to C:\ProgramData\failed_rdp.log, named the log FAILED_RDP_WITH_GEOLOCATION, and created it, allowing Azure to ingest and process the logs for further analysis

<img width="500" alt="Screen Shot 2024-09-10 at 3 08 53 PM" src="https://github.com/user-attachments/assets/6246bf58-7555-4cb5-8943-f6680e212cde">

## Step 11: Create Custom Fields Using Kusto Query Language (KQL)
Due to the deprecation of the "extract fields" option in Azure, I found a KQL solution online to create custom fields from the raw custom log data in Azure Sentinel.
- In Azure Sentinel, I navigated to Logs and used the KQL I found to create custom fields. I filled in the correct information for the fields I needed to extract, such as IP addresses and geographic details, ensuring the custom fields were accurately defined for further analysis.

<img width="650" alt="Screen Shot 2024-09-10 at 3 48 38 PM" src="https://github.com/user-attachments/assets/040db8d1-942d-48b7-b894-cf9ee882f1ab">


## Step 12: Map the Attack Data in Microsoft Sentinel
To visualize the attack data, I first went to Microsoft Sentinel and navigated to Workbooks.
- I added a new workbook, removed the default widgets, and added my query to visualize the failed RDP login attempts.

<img width="1440" alt="Screen Shot 2024-09-10 at 3 43 51 PM" src="https://github.com/user-attachments/assets/35f6a2d2-f9a3-4850-9d8d-1a1e14bf8659">

- Once the results started appearing, I edited the Visualization settings to display the data on a map.
- After selecting the map, I configured the map settings using latitude and longitude data, ensuring the geographic locations of the attacks were accurately represented.

<img width="300" alt="Screen Shot 2024-09-10 at 3 49 56 PM" src="https://github.com/user-attachments/assets/c29d9a89-8b28-4343-bf98-cbd38a1feb6a"> <img width="300" alt="Screen Shot 2024-09-10 at 3 50 10 PM" src="https://github.com/user-attachments/assets/b04b1e7e-50d8-40cd-afee-054387c0621f">

-I then saved the workbook as "Failed RDP World Map" and continuously refreshed it to display additional incoming attacks.

<img width="1217" alt="Screen Shot 2024-09-10 at 3 49 18 PM" src="https://github.com/user-attachments/assets/881d2190-d48f-4d41-9f7e-9739319c7cd5">

> The Final Product!
