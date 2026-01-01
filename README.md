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
SSH port 22 (for Linux administration).
RDP port 3389 (for Windows remote access).
Port 8000 (for Splunk web interface) For security during testing, set the Source for all connections to "My IP" to restrict access to your current location.

<img width="1607" height="702" alt="image" src="https://github.com/user-attachments/assets/f5a290fc-8753-4a35-b09f-a0174fa735a5" />
