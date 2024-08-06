# Azure-Honeypot-Lab

## Objective

I will be exposing a VM in Azure to the entire internet by modifying the Network Security Group (NSG) to allow all inbound traffic from the internet to the VM. This allows my device to be attacked from the internet. I will use a script on the VM to pull 4624 and 4625 event logs (successful auths and failures on the VM) from the VM and enhance the data utilizing IPGeolocation's API. I will then import the logs from the system in to Log Analytics Workspace where I will parse the raw data and then visualize it in Azure Sentinel Workbooks.

![image](https://github.com/user-attachments/assets/d4bf1264-e920-4730-805b-f5306100abb3)

My personal goal with this lab is to gain experience in Azure. Specifically, I wanted to highlight some of my KQL knowledge, which I learned from Microsoft Defender's Advanced Hunting tool. The next thing I wanted to show in this lab is how to interact with an API and use it in a real world scenario to equip security analysts with even more information provided by this API. Lastly, I wanted to get more experience with the cost management side of Azure. I set up budget's as well as alerting which are critical task's when dealing with cloud environments.




### Skills Learned

- Azure
- Windows Security Event Logs (4624 & 4625)
- IPGeolocation API
- KQL
- Powershell
- Data Visualization
- Microsoft Sentinel Workbooks
- Log Analytics Workspace
- Azure NSG

## Network Diagram Overview
 *Ref 1: Network Diagram*


The ne

## Steps
