## Objective

I will be exposing a VM in Azure to the entire internet by modifying the Network Security Group (NSG) to allow all inbound traffic from the internet to the VM. This allows my device to be attacked from the internet. I will use a script on the VM to pull Windows Security Event Logs (successful auths and failures on the VM) from the VM and enhance the data utilizing IPGeolocation's API. I will then import the logs from the system in to Log Analytics Workspace where I will parse the raw data and then visualize it in Azure Sentinel Workbooks (See Reference 2 & 3 below). Data visualizations are important in a security operations center as they can improve incident response times, provide quick analytics, and may even help make the data more actionable.
<br></br>
<br>
 *Ref 1: Network Diagram Overview*</br>
![image](https://github.com/user-attachments/assets/d4bf1264-e920-4730-805b-f5306100abb3)
<br>*Ref 2: World Heat Map Visualization*</br>
![image](https://github.com/user-attachments/assets/003a24cd-d57d-49dd-991d-664a8dde5c75)
<br></br>
<br>*Ref 3: Top 10 Usernames Attempted*</br>
![image](https://github.com/user-attachments/assets/1e5c85a0-8905-4b2f-bf19-da93ad277065)
<br></br>
My personal goal with this lab is to gain experience in Azure. Specifically, I wanted to highlight some of my KQL knowledge, which I learned from Microsoft Defender's Advanced Hunting tool. The next thing I wanted to show in this lab is how to interact with an API and use it in a real world scenario to equip security analysts with even more information provided by this API. Lastly, I wanted to get more experience with the cost management side of Azure. I set up budget's as well as alerting which are critical task's when dealing with cloud environments.

### Skills Learned

- Azure
- Windows Security Event Logs
- IPGeolocation API
- KQL
- Powershell
- Data Visualization
- Microsoft Sentinel Workbooks
- Log Analytics Workspace
- Azure NSG

## Step 1: Create a Budget in Azure
<br></br>
Under Cost Management, I went ahead and navigated to budgets for Azure Subscription 1.

 ![image](https://github.com/user-attachments/assets/9dda5044-4f13-47db-8170-9c451f7d217f)

My budget for this lab is 50$, this is the maximum I want to spend on this lab. I set the Name of the budget, reset period, and the creation/expiration dates. (The name says "onefiftydollars" this can be ignored)

![image](https://github.com/user-attachments/assets/3f226b05-00cc-413b-a6ad-00912f878c6d)

Forecasted cost alerts provide advanced notification that your spending trends are likely to exceed your allocated budget.
![image](https://github.com/user-attachments/assets/6cda7ced-0d8c-4239-9876-835f7c34f403)

## Step 2: Create Windows 10 Honeypot VM
<br></br>
In Azure you can spin up a virtual machine by typing "VM" on the search bar. You can then hit create, and then "Azure Virtual Machine"
Creating the VM and setting the region where this VM will be hosted. No redundancy is required as this is not a critical system, it's just a lab. Standard Windows 10 build.
 
 ![image](https://github.com/user-attachments/assets/33a00e5b-6053-48b6-ab39-884915ba50a8)


Exposing the VM to the internet.
Here I created a NSG rule to allow all inbound traffic to the VM as this is a honeypot. Honeypot NSG Inbound rule “anytrafficinfrominternet”.
 ![image](https://github.com/user-attachments/assets/87e99ece-8f6d-4d8b-9fcf-8c4c0fab9059)


VM has been deployed, now testing the VM by using RDP from my local system.

![image](https://github.com/user-attachments/assets/2fcfa930-f6b5-40e3-9c0e-decf6d145bc0)

## Step 3: Log Analytics Workspace
<br></br>
Here I create my log analytics workspace to manage the logs I will be pulling from the honeypot.
![image](https://github.com/user-attachments/assets/8a8f9d56-1992-4ecb-b225-9d5ccd5f30e8)

Enabled all events for all my servers in Defender for Cloud. 

![image](https://github.com/user-attachments/assets/479c68ba-c907-4e17-8a85-c732382c5b21)

Then I connected my Log Analytics Workspace to my honeypot VM.

![image](https://github.com/user-attachments/assets/e61d5c04-6388-4375-882b-5e94ff698408)
![image](https://github.com/user-attachments/assets/271d7a52-d253-458b-a13d-8ae2418e6d50)

## Step 4: Log Analytics Workspace
<br></br>
I navigated to Azure Sentinel where I created my Sentinel instance and then added it to my Log Analytics Workspace.
![image](https://github.com/user-attachments/assets/2e29e52c-d4d2-4aed-9339-0af6729ea6a5)
![image](https://github.com/user-attachments/assets/74a1a1ef-f1fe-416b-9472-80005f844c84)


## Step 5: IPGeolocation API and Log Analytics Workspace Log Collection
<br></br>
The API that I decided to go with is the IPGeolocation API as it provides longitude/latitude data that is required for the map visualization in Sentinel. It's also one of the most amount of API call's for $15.
![image](https://github.com/user-attachments/assets/e015e177-5580-417b-b9f6-6e9047da62b6)

With the help of ChatGPT, I used a powershell script that interacts with IPGeolocation's API, and outputs the information to a log file in "c:\programdata\". The 3 events observed are test events from my hotspot to make sure it can be reached from the internet.
Each entry will look like this: "latitude:$($latitude),longitude:$($longitude),destinationhost:$($destinationHost),username:$($username),sourcehost:$($sourceIp),state:$($state_prov), country:$($country),label:$($country) - $($sourceIp),timestamp:$($timestamp)"
![image](https://github.com/user-attachments/assets/b7fcd241-93be-4972-b85b-f15d4e79d496)

Created a custom log in Log Analytics Workspace and pointed it to the log file in "c:\programdata\". I named the table associated with these logs "Failed_RDP_With_Geo_CL". This allows us to manipulate the data using KQL.
![image](https://github.com/user-attachments/assets/829ca5f7-6c11-4d0f-922b-7893fe045294)

The raw data needs to be parsed so that each latitude, longitude, destinationhost, username, sourcehost, state, country, label, have their own column. I spent some time messing around with the parse RawData field and got it to work by using this query in KQL. There are some results because the machine is currently exposed and being scanned from the internet.
![image](https://github.com/user-attachments/assets/448c796a-1da6-4ff1-8f09-b54b0db0f2da)

Here is a copy of my KQL query: 
<br></br>
Failed_RDP_With_GEO_CL
| parse RawData with * "latitude:" Latitude ",longitude:" Longitude ",destinationhost:" DestinationHost ",username:" Username ",sourcehost:" Sourcehost ",state:" State ", country:" Country ",label:" Label ",timestamp:" Timestamp 
| extend EventCount = 1
| summarize event_count = sum(EventCount) by Latitude, Longitude, DestinationHost, Username, Sourcehost, State, Country,Label, Timestamp
| project Latitude, Longitude, DestinationHost, Username, Sourcehost, State, Country, Label, Timestamp

## Step 6: Azure Sentinel Workbook/ World Heat Map Visualization
<br></br>
Finally we will go to Microsoft Sentinel in Azure and create our workbook, this will allow us to visualize our data using KQL.
![image](https://github.com/user-attachments/assets/af0d18da-158d-4581-af56-c362759d57a2)
![image](https://github.com/user-attachments/assets/34902b7f-5912-437c-a728-52f6435a5b55)

Here I added in our KQL query with a couple of adjustments. Some of the adjustments that were made is that I created a event_count column. This column sums up every single entry and is the sum of an eventcount which is equal to one. This will give the heat map circle’s their size. (More hits means a bigger circle due to the event_count column). The map settings to the right are pretty self explanatory. Most of the IP’s observed hitting our honeypot have a geolocation of Saudi Arabia followed by Oman, Japan, and Bulgaria. 

![image](https://github.com/user-attachments/assets/9dc1c58b-80d6-476b-9af2-a22c59c80129)

World Attack Map after just 24 hours! Most of the attacks are coming from Indonesia, Japan, Oman and then Saudi Arabia.
![image](https://github.com/user-attachments/assets/003a24cd-d57d-49dd-991d-664a8dde5c75)

BONUS:
I wanted to find out which top 10 usernames are being used to bruteforce this VM. I created another visualization to list that information. This was not part of the original plan for this lab however, I figured it would be good information to have.
<br></br>
![image](https://github.com/user-attachments/assets/1e5c85a0-8905-4b2f-bf19-da93ad277065)
<br></br>

Here is the KQL query I used to pull that information:
<br></br>
Failed_RDP_With_GEO_CL
| parse RawData with * "latitude:" Latitude ",longitude:" Longitude ",destinationhost:" DestinationHost ",username:" Username ",sourcehost:" Sourcehost ",state:" State ", country:" Country ",label:" Label ",timestamp:" Timestamp 
| summarize Count = count() by Username
| top 10 by Count desc

Lastly, I wanted to create a chart that shows all 4624, successful auths on the local account I created. I filtered by username, and projected the columns I found most important. This is extremely important as I need to know when there is an authentication as the only authentications should be coming from my public IP address (which they are).

![image](https://github.com/user-attachments/assets/b4d8b5ba-9ea7-41cb-b918-cf7d01d5e479)

Here is a snippet of the KQL query.

SecurityEvent
| where EventID == 4624
| where Account contains "honeypotadmin"
| project TimeGenerated, Account, Computer, Activity, IpAddress, LogonTypeName
| sort by TimeGenerated desc

Honeypot Visualizations in Sentinel
<br></br>
![image](https://github.com/user-attachments/assets/c744aeac-6fa6-40f7-b10c-645738b843b3)


<br></br>
##  Conclusion
<br></br>
The final visualizations include a "World Heat Map" & "Top 10 Usernames attempted". These visualizations are a great source of information because they make complex data easier to read. The hardest challenge during this lab was the creation of the powershell script that was used to interact with the IPGeolocation API. I would have liked to store my API key in an HSM since it's best practice however, for the purposes of this lab I think it gets the job done. The most interesting part for me was creating the KQL queries as I had already had some experience with it. In the end, I learned a lot from interacting with API's, managing logs in Log Analytics Workspace, and data visualizations in Azure Sentinel. I highly recommmend this lab to anyone looking to get some experience in any of these areas.
<br></br>
