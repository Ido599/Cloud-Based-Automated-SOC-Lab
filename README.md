# Cloud-Based-Automated-SOC-Lab


## Objective

This project simulates a real-world enterprise environment by deploying a domain-controlled Windows network with centralized logging in Splunk Enterprise, and integrating automated response workflows using Shuffle.io and Slack. It demonstrates identity management, log ingestion, SIEM analysis, alerting, and automated incident response (SOAR).

Key objectives:

- Deploy Active Directory for centralized authentication and domain management
- Forward Windows Event Logs into Splunk for security monitoring
- Detect and alert on successful RDP logins (Event ID 4624)
- Automate alert triage and user account disablement using Shuffle + Slack


### Skills Learned

- Splunk installation and configuration in educational environments.
- Splunk data ingestion from multiple sources and log formats.
- Creation and management of Splunk alert rules for automated threat detection.
- ultr cloud environment setup and configuration for security lab deployments.
- Ability to design and implement automated incident response workflows using Shuffle.
- Development of skills in creating custom playbooks for threat detection and automated alerting.
- Experience with real-time notification systems and escalation procedures in SOC environments.
- Understanding of security tool integration and workflow orchestration.
- mproved ability to streamline security operations through automation and reduce manual response times.

### Tools Used

- Vultr - Cloud infrastructure provider for hosting the lab environment (Windows Server, Windows Client, Ubuntu)
- Vultr Network Firewall - Cloud-based firewall rules for secure access control.
- Linux Ubuntu - Operating system platform for Splunk deployment.
- Windows Server 2022 - Two separate servers: one dedicated to AD deployment and one as a test machine.
- Active Directory - Directory service for user authentication and security event generation.
- Splunk - SIEM platform for log ingestion, analysis, and alert management.
- Shuffle - SOAR platform for security orchestration and automated response workflows.
- Slack - Team communication platform for real-time incident notifications.
- APIs - For integrating different security tools and platforms.

## Project Walkthrough

### Step 1: Project Architecture Design 

Create Vultr account and design network diagram
<img width="1632" height="640" alt="DRAW" src="https://github.com/user-attachments/assets/02246f23-19e1-4d26-858f-f8df9cd2243d" />

### Step 2: Virtual Machine Deployment on Vultr
1.Deploy 3 VMs:
- Domain Controller: Windows Server 2022 (2 vCPU / 4 GB RAM / 80GB Storage).
- Test Machine: Windows 10/11 (1 vCPU / 2 GB RAM / 55GB Storage).
- Splunk Server: Ubuntu 22.04 (4 vCPU / 8 GB RAM / 160GB Storage).
- Disable automatic backups on each machine before deploying.

<img width="1606" height="420" alt="image" src="https://github.com/user-attachments/assets/2af438af-86ba-4ee5-bec6-806f04366217" />

2.Create new firewall groups:
- SSH port 22 (for Linux administration).
- RDP port 3389 (for Windows remote access).
- Port 8000 (for Splunk web interface) For security during testing, set the Source for all connections to "My IP" to restrict access to your current location.

<img width="1607" height="702" alt="image" src="https://github.com/user-attachments/assets/f5a290fc-8753-4a35-b09f-a0174fa735a5" />

3.Enable VPC networking on each machine so VMs can communicate privately:
- Update Firewall Group assignment for each VM
- Make note of VPC IP address for each VM

  <img width="1608" height="655" alt="image" src="https://github.com/user-attachments/assets/64173c0d-777a-411f-8d44-3a0458bc7b3d" />

4.Assign static IPs to DC/Test machines for reliable DNS/domain setup:
- Once you remote in to each VM, right click on network icon at bottom right and select change network settings
- Right click ethernet instance 0 2 and select propertires
- Double click internet protocol version 4
- Select "Use the following IP address"
- Enter the VPC IP address and subnet mask for that machine

<img width="762" height="708" alt="image" src="https://github.com/user-attachments/assets/f76d7c10-e022-472d-8917-fbebc8c055ba" />

### Step 3:Active Directory Configuration
- Remote into Domain Controller VM.
- Open Server Manager and select Add Roles and Features.
- Install Active Directory Domain Services (AD DS).

  <img width="1038" height="788" alt="image" src="https://github.com/user-attachments/assets/bb7c9cf1-6347-4f81-ba28-5b741bc187a8" />

- Once finished, select the alerts icon at the top and click the alert that says "Promote server to Domain Controller". When prompted, create a new forest 

<img width="961" height="752" alt="image" src="https://github.com/user-attachments/assets/157ea301-5502-4e3a-9642-5754f0ef942f" />

- Once installation is complete, search for **Active Directory Users and Computers** 6. Inside your newly created directory, right click > new > User 7. Create a user

<img width="950" height="658" alt="image" src="https://github.com/user-attachments/assets/3fbc3ac7-74a9-425b-9d2d-2da73f4ca0ed" />

- RDP into the test machine next and search for This PC.
- Select Rename this PC (Advanced) > Change.
- Select Member of Domain and enter the domain name you created and the password.
- Join Test Machine to the domain (You may need to add DomainController IP address to DNS records).

<img width="1113" height="906" alt="image" src="https://github.com/user-attachments/assets/2ba4cf3c-3b03-4fcf-872e-cceecc866ba0" />

<img width="1447" height="873" alt="image" src="https://github.com/user-attachments/assets/4c85fd6d-b59f-4b5d-ba50-b073f319f707" />

You  may need to configure RDP permissions to allow users to remote in. Search for "Allow Remote Connections To This Computer" and add the user you created.

<img width="1076" height="827" alt="image" src="https://github.com/user-attachments/assets/7c74bffc-c55f-48c5-b73c-5c98755747ca" />

### Step 4:Splunk Installation & Configuration
- SSH into Splunk Linux system and update all repositories with apt-get update && apt-get upgrade 
- Install Splunk Enterprise (.deb package) by copying wget link and entering in CLI.
- Run the command: dpkg -i splunk-9.4.3-237ebbd22314-linux-amd64.deb cd /opt/splunk/bin/ ./splunk start 
- Navigate to Splunk file and run command ./splunk start
- Enable port 8000 on the Ubuntu firewall: ufw allow 8000
  <img width="1222" height="630" alt="image" src="https://github.com/user-attachments/assets/965a7b95-3297-4c13-a11c-c3a71e4210d1" />

- Access Splunk in the browser by using the address splunk-VM-ip-address:8000

<img width="1683" height="717" alt="image" src="https://github.com/user-attachments/assets/83ad2bd3-0c4f-4356-a32d-45b8e8d12ca5" />

- In Splunk Web, go to "Find more apps" and install Splunk Add-on for Microsoft Windows
- In settings, create index for Windows Security logs.

 <img width="1433" height="688" alt="image" src="https://github.com/user-attachments/assets/5a90c6c1-ab98-43ae-8bb9-cad9ed6b0bac" />

 Configuring Data Receiving (Port Forwarding).

<img width="1597" height="728" alt="image" src="https://github.com/user-attachments/assets/3666181c-9aae-4fae-9ce1-88425d820937" />

- Under "Receive data" section, click on Configure receiving.

<img width="1608" height="537" alt="image" src="https://github.com/user-attachments/assets/68de258e-3361-4534-9cab-03b5007a1f8a" />

- Click on New Receiving Port to create a new data input port and then click on save.

<img width="1606" height="425" alt="image" src="https://github.com/user-attachments/assets/9fccdb2e-6cd8-4f68-bfd8-64744b3a040c" />

### Step 5:Installing and Configuring Splunk Universal Forwarder on Windows Machines
- Log into Splunk Web on DC and Test Machine and install Splunk Universal Forwarders on both.
<img width="606" height="458" alt="image" src="https://github.com/user-attachments/assets/b8a613f2-2109-460e-973b-0caf3255bfb8" />

- In the receiving indexer configuration, enter the Splunk server's private IP address and port 9997 (the default Splunk forwarding port).
<img width="615" height="468" alt="image" src="https://github.com/user-attachments/assets/aca7a8dd-e633-4669-bbae-94a3fc26c3cc" />

Configuring Log Collection
- Now we need to configure which logs the forwarder will send to Splunk manually. Navigate to: C:\Program Files\SplunkUniversalForwarder\etc\system\default
- Copy the inputs.conf file from the default directory.

<img width="1460" height="767" alt="image" src="https://github.com/user-attachments/assets/c5a8499d-a860-42cb-9aad-4e827ada80e7" />

- Paste the inputs.conf file into: C:\Program Files\SplunkUniversalForwarder\etc\system\local

<img width="1336" height="672" alt="image" src="https://github.com/user-attachments/assets/70499e2c-8037-4e14-a2cb-60ea4a34dd3a" />
Open inputs.conf file in Notepad with Admin Permissions and scroll to the bottom. Add the following code:
[WinEventLog://Security]
index=<Put your index name>
disabled=false

<img width="1326" height="685" alt="image" src="https://github.com/user-attachments/assets/f12e6b6c-54d7-4a2c-8337-4665fca7ba22" />

- Search for Services on the machine and open the SplunkForwarder service.
- Select log on tab > check local system account, and apply
- Restart the service. If the restart fails, right-click again and choose Start. 

<img width="1606" height="856" alt="image" src="https://github.com/user-attachments/assets/e831dfd0-70be-45d6-ac00-748ce4879dbf" />

- Navigate back to Splunk and select Apps > Search and Reporting.
- In the query box, type index=< Put your index name> and hit search.
- Note: If you don't see anything, may need to update Vultr and host firewall rules for port 9997. Then try again. You should see logs.
- Query Event ID 4624 (successful logins) in Splunk.
- Refine query to detect RDP (Logon Type 7 and 10) logins:
 index=<Put your index name> EventCode=4624 (Logon_Type=10 OR Logon_Type=7) Source_Network_Address!="-"

<img width="1595" height="628" alt="image" src="https://github.com/user-attachments/assets/a2fc7e75-3758-4967-bbe3-c05c72261fa5" />

- Save query as an alert
- Runs every 1 minute (cron: * * * * *)
- Triggers on unauthorized RDP logins
- Severity: Medium

Back in Vultr Firewall settings, remove the RDP Rule and add it back, changing source to Anywhere

 <img width="1508" height="701" alt="image" src="https://github.com/user-attachments/assets/2ef34f50-d8f9-453a-9ada-5c135a934e02" />

### Step 6: SOAR Automation with Shuffle and Slack Integration
- Create new workflow in Shuffle.
<img width="612" height="455" alt="image" src="https://github.com/user-attachments/assets/1280aa10-7c43-4a19-b285-b7b1f97675a7" />

- Configure Webhook named "Splunk-Alert".
<img width="350" height="435" alt="image" src="https://github.com/user-attachments/assets/48d10045-9ca0-449c-9b2e-727979aa3a7e" />

- Copy the webhook URI, navigate to your alert in Splunk > Edit > Add Action > Webhook > Paste URI > Save
  <img width="1602" height="697" alt="image" src="https://github.com/user-attachments/assets/5006aff6-7f8d-4764-a192-57714756e884" />

 Adding HTTP Integration
-In Shuffle's search field (top left), type "HTTP", click on it, and drag to the work area.
<img width="1610" height="632" alt="image" src="https://github.com/user-attachments/assets/57b2e13c-cbde-484e-9451-482dd33bae57" />


- Create a new channel in Slack called #alerts
- Go to the Slack API dashboard and select your workspace.
- Navigate to Incoming Webhooks in the sidebar and toggle the feature to On.
- Click Add New Webhook to Workspace, select the desired channel (e.g., #alerts), and click Allow.
- Copy the Webhook URL generated (it will look like

<img width="1595" height="552" alt="image" src="https://github.com/user-attachments/assets/280b6d21-719a-4fb3-b0b1-78de558117bc" />

- Open your Shuffle Workflow and locate the HTTP Node or the dedicated Slack App Node.
- In the Node settings, set the URL field to the Webhook URL you copied from the Slack API.

<img width="1582" height="642" alt="image" src="https://github.com/user-attachments/assets/881981a1-6398-409d-9875-67f6bdd735eb" />

- Rerun one of the logs from Explore Runs to test the complete integration.
<img width="506" height="592" alt="image" src="https://github.com/user-attachments/assets/01777012-6255-42be-a976-e27996276b3a" />
### NEED FOR HTTP WITH 200 OK PICTURE


