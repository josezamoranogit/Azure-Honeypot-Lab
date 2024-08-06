# Azure-Honeypot-Lab

## Objective

I will be exposing a VM in Azure to the entire internet by modifying the Network Security Group (NSG) to allow all inbound traffic from the internet to the VM. This allows my device to be attacked from the internet. I will use a script on the VM to pull 4624 and 4625 event logs (successful auths and failures on the VM) from the VM and enhance the data utilizing IPGeolocation's API. This will allow me to manipulate the data in Log Analytics Workspace where I will parse the raw data and visualize it in Azure Sentinel Workbooks.
My personal goal with this lab is to gain experience in Azure. Specifically, I wanted to highlight some of my KQL knowledge. My experience with KQL has mainly been with the Microsoft Defender Advanced Hunting tool. The next thing I wanted to show in lab is how to interact with an API and use it in a real world scenario to equip security analysts with even more information provided by this API. Lastly, I wanted to get more experience with the cost management side of Azure. I set up budget's as well as alerting which are critical task's when dealing with cloud environments.



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

![image](https://github.com/user-attachments/assets/d4bf1264-e920-4730-805b-f5306100abb3)

The ne

## Steps
