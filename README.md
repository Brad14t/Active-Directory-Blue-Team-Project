# Active Directory Blue Team Project
This project is me practicing my blue team skills and prepairing for when I set up a more in depth physical homelab within my home. Testing which software stack I would like to use.

Specifically this project is an Active Directory enviorment, detecting unauthorized logins via Splunk, and using Shuffle SOAR to responde to the attacks.

An Attack or someone trying to login to my Windows servers, the telemetry will sent to splunk for the soc analyst to view, this will trigger an alert within splunk, this will send a notification to my slack board, next it will automatically trigger a playbook within my Shuffle SOAR asking the soc analyst via email if they want to disable the user, if no nothing happens, if yes the domain user will be disabled. If the user is disabled a final notification will be sent to the soc analyst, stating the user was disabled.

# Software Used
* Active Directory
* Splunk
* Shuffle
* Vultur

# Lab Setup
* Windows Server as my Domain Controller
* Ubuntu Server for Splunk
* Windows Server as the Test Server for the attackers
* Physcial Laptop used by me (Soc Analyst) to access VM's 
* Attacker Windows VM or VPN used to generate attacker data

<img width="540" height="615" alt="Screenshot 2025-07-23 095323" src="https://github.com/user-attachments/assets/674abb69-fb0e-41e5-a141-1cbed4fac4e9" />

# The Process

In this portian I will breifly describe what I did, this will not be an in depth every step of the way approach, due to many troubleshooting steps and its fun to discover them along the way.

**Domain Controller**

First I am going to create a Windows Server VM within Vultur. This will be my domain controller for this lab.

<img width="806" height="467" alt="Screenshot 2025-06-25 115622" src="https://github.com/user-attachments/assets/b10fd373-1745-4879-a123-652f4fadab90" />

<img width="594" height="647" alt="Screenshot 2025-06-25 115631" src="https://github.com/user-attachments/assets/113400c1-5b40-468b-b607-0c4dc71fa004" />

<img width="1258" height="675" alt="Screenshot 2025-06-25 115646" src="https://github.com/user-attachments/assets/0efd8023-b2e4-4be8-ab2f-85b46b2c9a8e" />

<img width="1005" height="719" alt="Screenshot 2025-06-25 115728" src="https://github.com/user-attachments/assets/2a3ff482-d505-42d9-b8cc-e0433e0d7131" />

<img width="1272" height="228" alt="Screenshot 2025-06-25 115810" src="https://github.com/user-attachments/assets/1da68ebc-d358-458e-922b-0e1ecebd61df" />

Now I have a Windows Server named MYADProject-ADDC01

**Test Server**

This will be one of the machines that are going to be attacked, and sending telemetry to Splunk. It wont be as demanding as the domain controller so I made the VM smaller.

<img width="1241" height="466" alt="Screenshot 2025-06-25 115937" src="https://github.com/user-attachments/assets/430152d7-6d9d-4d22-a4e2-ba4fce69ab71" />

<img width="1235" height="568" alt="Screenshot 2025-06-25 115959" src="https://github.com/user-attachments/assets/0d5e6ccf-4418-4865-9044-f3ba8fe7a40c" />

**Splunk Server**

Next will be creating an Ubuntu server spun up, this will be my dedicated Splunk server.

<img width="1264" height="674" alt="Screenshot 2025-06-25 120303" src="https://github.com/user-attachments/assets/9eb73f6e-0a61-4d7e-aa56-8ead458f68af" />

<img width="1264" height="526" alt="Screenshot 2025-06-25 120330" src="https://github.com/user-attachments/assets/ca6b212b-e397-4d1c-9fea-bd1f5831dce9" />

<img width="1275" height="272" alt="Screenshot 2025-06-25 120356" src="https://github.com/user-attachments/assets/d6fe1280-a49e-4327-862e-acb890798db2" />

Finally with all 3 created, within Vultur I will get them started up.

<img width="1370" height="559" alt="Screenshot 2025-06-25 120443" src="https://github.com/user-attachments/assets/63916283-a873-4a65-a494-4782af7c4fc4" />

# Firewall Creation 

First I want to make sure my VM's are secure, and that only my machines can access each other.

Within Vultur I will go create a few rules:

` SSH port 22 will only be accepting my public IP `

` RDP port 3389 will only be accepting my public IP `

` Drop any other port is by default `

# VPC 

Next I will create a virtual private network, this will allow me to have a logically seperated part of the cloud, giving me a private network.

<img width="419" height="316" alt="Screenshot 2025-06-25 142410" src="https://github.com/user-attachments/assets/d9d0ef86-21af-47ac-934d-a8a9760591ec" />

<img width="1111" height="375" alt="Screenshot 2025-06-25 142752" src="https://github.com/user-attachments/assets/be6c7cee-28a1-4075-bc96-25aa7337ea5b" />

<img width="388" height="279" alt="Screenshot 2025-06-25 142846" src="https://github.com/user-attachments/assets/4287151e-0bea-411e-ae58-613771149728" />

<img width="378" height="373" alt="Screenshot 2025-06-25 143015" src="https://github.com/user-attachments/assets/8168c199-d94c-475e-beed-aafc73b95c1c" />

<img width="373" height="304" alt="Screenshot 2025-06-25 143357" src="https://github.com/user-attachments/assets/2b1bc785-0c17-40f3-93a8-7538f331b54b" />

<img width="816" height="744" alt="Screenshot 2025-06-25 143426" src="https://github.com/user-attachments/assets/582b8bdc-c2ce-4369-9384-6ab0c2970e8e" />

After the internal network was established. Trying to send pings from the Splunk Server to the test server failed. 

<img width="406" height="149" alt="Screenshot 2025-06-25 144526" src="https://github.com/user-attachments/assets/e5e98f8f-661e-44b4-b526-3e1ede23c37f" />

To correct this I will set a static IP address for the test server. Inputting the newly created VPC internal addresses.

<img width="351" height="213" alt="Screenshot 2025-06-25 150239" src="https://github.com/user-attachments/assets/ef583813-3766-450e-af7c-02a850c29a3a" />

<img width="207" height="249" alt="Screenshot 2025-06-25 150321" src="https://github.com/user-attachments/assets/bc7bd0de-76a8-4177-98c0-31260fc1cfc5" />

<img width="223" height="254" alt="Screenshot 2025-06-25 150538" src="https://github.com/user-attachments/assets/cb19d3d7-20f2-41bc-af4f-069bcdf185d9" />

After this change I was able to receive pings.

<img width="915" height="551" alt="Screenshot 2025-06-30 101411" src="https://github.com/user-attachments/assets/44c3e0c4-c67f-446b-8f3b-2dda2604047d" />

# Domain Controller Configuration








