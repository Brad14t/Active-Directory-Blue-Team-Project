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

First I will add managment tools 

<img width="297" height="285" alt="Screenshot 2025-06-30 101702" src="https://github.com/user-attachments/assets/07977b50-84bd-4a8e-8fa3-8b3a29823799" />

<img width="439" height="288" alt="Screenshot 2025-06-30 102002" src="https://github.com/user-attachments/assets/19a74b0d-a9b7-493a-837d-94ecd2f664ac" />

<img width="321" height="311" alt="Screenshot 2025-06-30 102152" src="https://github.com/user-attachments/assets/6fd08527-c8ea-4470-89d8-ce6df6767c64" />

<img width="426" height="317" alt="Screenshot 2025-06-30 102357" src="https://github.com/user-attachments/assets/b1b77286-c233-440e-ac84-5921fe63186c" />

<img width="423" height="314" alt="Screenshot 2025-06-30 102557" src="https://github.com/user-attachments/assets/2a9423b6-30d1-464b-a281-26fd0eaeb7ff" />

Now with that done I can access Active Directory services.

Next I will setup a user for future testing. I will name this user Jenny Smith.

<img width="650" height="555" alt="Screenshot 2025-06-30 114245" src="https://github.com/user-attachments/assets/e5110da4-1f2e-4211-8de9-17f9a62482ae" />

Next I will set a static DNS for my test server.

<img width="505" height="196" alt="Screenshot 2025-06-30 140310" src="https://github.com/user-attachments/assets/4316e8e6-1918-422a-879c-2a00816ad72e" />

<img width="231" height="265" alt="Screenshot 2025-06-30 140640" src="https://github.com/user-attachments/assets/120a259e-9c16-45e8-b48e-eece6b712394" />

<img width="324" height="176" alt="Screenshot 2025-06-30 140939" src="https://github.com/user-attachments/assets/06280a35-58b7-47fc-a852-bf934a7921fa" />

Next I will sign in as Jenny and make sure I can RDP to the server, and that Jenny is in the remote desktop group.

<img width="784" height="588" alt="Screenshot 2025-06-30 141308" src="https://github.com/user-attachments/assets/67b621a9-2d47-4714-ac6c-ac40e233bccb" />

<img width="375" height="372" alt="Screenshot 2025-06-30 141727" src="https://github.com/user-attachments/assets/40edfe2b-0d8c-457a-9f69-52c74473c859" />

<img width="292" height="193" alt="Screenshot 2025-06-30 141741" src="https://github.com/user-attachments/assets/0ed32541-4a0b-4b69-958c-5c995ccd1629" />

<img width="349" height="204" alt="Screenshot 2025-06-30 141816" src="https://github.com/user-attachments/assets/56da831c-6d71-49f1-a4a0-1a8fb89a8f28" />

# Splunk Configuration

For setting uo Splunk I will start by SSHing into the Ubuntu server. And I will make sure the system is full updated. To do this I run:

` apt-get update && apt-get upgrade `

After updates are complete I will go to Splunks website to get the Linux download. I will get the wlink so I may paste it into my SSH session.

<img width="886" height="211" alt="Screenshot 2025-06-30 142848" src="https://github.com/user-attachments/assets/8ba9f758-fe38-4c56-8c87-4b88e79612cc" />

<img width="1735" height="93" alt="Screenshot 2025-06-30 142949" src="https://github.com/user-attachments/assets/9ecb7168-ac2f-4f10-bcee-0a2673ee6482" />

Now after the download is complete I will look for ` ./splunk `

The correct path is ` cd /opt/splunk/bin/ `

<img width="1648" height="252" alt="Screenshot 2025-06-30 143519" src="https://github.com/user-attachments/assets/49d8e918-3986-4ec2-a012-d057d96cf8e6" />

<img width="183" height="51" alt="Screenshot 2025-06-30 143543" src="https://github.com/user-attachments/assets/2ddefc17-adcc-413c-8a6b-7197bfff77b2" />

After finding the splunk file, run this command to start it: ` ./splunk start `

<img width="672" height="53" alt="Screenshot 2025-06-30 143606" src="https://github.com/user-attachments/assets/5fb1d124-b30f-46a5-8cd9-b18828f84363" />

After starting Splunk, it wont be able to communicate with my networks since Splunk communicates over port 8000. To fix this I will adjust my firewall.

<img width="320" height="58" alt="Screenshot 2025-06-30 143837" src="https://github.com/user-attachments/assets/290759c8-8a51-421d-ac0e-07966ac54eef" />

In Vulture I will add the rule ` Allow port 8000 from my IP address `

<img width="513" height="229" alt="Screenshot 2025-06-30 143931" src="https://github.com/user-attachments/assets/1d22ac3a-ef9a-4835-bd43-0711b67e0217" />

Then I will add this rule to my Splunk Server as well with this command: ` ufw allow 8000 `

<img width="668" height="130" alt="Screenshot 2025-06-30 143958" src="https://github.com/user-attachments/assets/c7b3588d-cb28-423c-ab18-13010806a15b" />

Now I can acces my Splunk instance

<img width="1186" height="416" alt="Screenshot 2025-06-30 144216" src="https://github.com/user-attachments/assets/4187cafd-6488-461c-ac61-cddf69fd1d38" />

Now to make things easier for myself and allow data to be ingested the correct way to Splunk I need to add an app within Splunk.

I will download and install Splunk add on for Windows.

<img width="256" height="190" alt="Screenshot 2025-06-30 144334" src="https://github.com/user-attachments/assets/c050bd0b-558c-4071-8587-980f688c9d93" />

<img width="706" height="202" alt="Screenshot 2025-06-30 144411" src="https://github.com/user-attachments/assets/f76aede2-e862-4ce1-9199-12befbb2e717" />

Next is to set up an index for the data that will be coming from the Windows Servers

<img width="457" height="198" alt="Screenshot 2025-06-30 144555" src="https://github.com/user-attachments/assets/5422abcf-e8a2-405c-9ceb-706b868e71af" />

<img width="597" height="375" alt="Screenshot 2025-06-30 144629" src="https://github.com/user-attachments/assets/cbbc1947-cd8f-493d-aa81-db4c84fc4711" />

Now I have to set a receiving port so the data reaches me. The default, which I use is ` 9997 `

<img width="456" height="147" alt="Screenshot 2025-06-30 144657" src="https://github.com/user-attachments/assets/349bb7d3-de38-4888-8f5a-0fac6e05a0e9" />

<img width="321" height="219" alt="Screenshot 2025-06-30 144859" src="https://github.com/user-attachments/assets/984bfa98-f71d-4e2c-bce4-0e5e30576114" />

<img width="697" height="171" alt="Screenshot 2025-06-30 144925" src="https://github.com/user-attachments/assets/71f0b112-86df-4a32-a475-0a3a85d7ad73" />

After setting the port I will need to install Splunk Universal Forwarder on each Windows server.

On the test server and domain controller I will install the same way.

<img width="894" height="108" alt="Screenshot 2025-06-30 145225" src="https://github.com/user-attachments/assets/cac8c348-3835-4812-932f-99b837e7b6a0" />

<img width="758" height="594" alt="Screenshot 2025-06-30 150408" src="https://github.com/user-attachments/assets/0214364c-fbe8-428d-b552-aaf23914f855" />

<img width="375" height="292" alt="Screenshot 2025-06-30 150526" src="https://github.com/user-attachments/assets/e4d77ba1-3d79-4cdf-b371-335e400b82e5" />

<img width="376" height="298" alt="Screenshot 2025-06-30 150651" src="https://github.com/user-attachments/assets/bba209b7-c774-45f2-86f2-b798054e3a88" />

Next I will need to make sure I have the ` inputs.conf ` file. 

First I check to see if I have it.

<img width="1113" height="317" alt="Screenshot 2025-06-30 151218" src="https://github.com/user-attachments/assets/c85f5691-5d1a-4141-a165-f56143fc8bc1" />

If not, I can copy and paste it from the default folder 1 step back.

<img width="478" height="160" alt="Screenshot 2025-06-30 151414" src="https://github.com/user-attachments/assets/37f6b29a-7fbc-4850-9537-55a20d729975" />

<img width="242" height="190" alt="Screenshot 2025-06-30 151445" src="https://github.com/user-attachments/assets/57f79079-c7be-4dad-b2ab-fce14a84a1f1" />

<img width="787" height="339" alt="Screenshot 2025-06-30 151514" src="https://github.com/user-attachments/assets/a99ffb8d-07ac-439a-95f5-6e02025cb919" />

Once I have the ` inputs.conf ` file in my local folder, I will need to edit the file in notepad as admin.

<img width="247" height="210" alt="Screenshot 2025-06-30 151532" src="https://github.com/user-attachments/assets/250c2ec8-cbb0-4e91-9fae-b5629a4f3612" />

<img width="199" height="269" alt="Screenshot 2025-06-30 152040" src="https://github.com/user-attachments/assets/5e3b3acf-02cf-4767-8ac8-872088ef575b" />

Next I will need to edit the Splunk Service, after going into my system services aws admin. I will find the Splunk Forwarder and change it to login as a local system account.

<img width="232" height="385" alt="Screenshot 2025-06-30 152112" src="https://github.com/user-attachments/assets/b7d15c5d-7670-4eb9-ad72-be2b3d644da8" />

<img width="385" height="330" alt="Screenshot 2025-06-30 152236" src="https://github.com/user-attachments/assets/97b39652-01c2-4ff2-a367-eb8313bb5454" />

<img width="323" height="357" alt="Screenshot 2025-06-30 152315" src="https://github.com/user-attachments/assets/1601bb0c-e310-4746-bffb-55e1b25857b3" />

Next stop or restart the Splunk service, then select start.

<img width="379" height="321" alt="Screenshot 2025-06-30 152442" src="https://github.com/user-attachments/assets/8f9dabcc-93dd-4309-a55d-66aca1ab3aa8" />

After that I check if I am receiving telemetry, I see that I cant see anything.

<img width="657" height="305" alt="Screenshot 2025-06-30 152601" src="https://github.com/user-attachments/assets/dea20cd5-78ad-4142-9d31-c60bf6529768" />

After I realized I never added the 9997 port rule to my SSH session, I run ` ufw allow 9997 `

<img width="674" height="87" alt="Screenshot 2025-06-30 152630" src="https://github.com/user-attachments/assets/b94b0b4c-4c0a-412c-a789-5f47db2f5640" />

Now I am able to see the data.

<img width="562" height="593" alt="Screenshot 2025-07-08 102127" src="https://github.com/user-attachments/assets/d8f7dad8-f4d5-401b-ac7e-d1dc9b2fb4a0" />

<img width="894" height="372" alt="Screenshot 2025-07-08 102136" src="https://github.com/user-attachments/assets/7075a015-5ec2-4757-bcd9-9661f50bfcd5" />

After seeing the data, I start to generate some false "attacks" by RDPing into the test server via VPN's, this way it gives me different ip addresses.

To simulate some attacks I added a new rule to my firewall, opening RDP port 3389 to anyone.

<img width="524" height="292" alt="Screenshot 2025-07-18 141539" src="https://github.com/user-attachments/assets/93229c9f-2321-4162-ac77-bbf4fbd7c591" />

<img width="653" height="125" alt="Screenshot 2025-07-08 102328" src="https://github.com/user-attachments/assets/91bb138c-56e3-4732-b685-2d0d9c03a3ea" />

<img width="1325" height="609" alt="Screenshot 2025-07-08 141026" src="https://github.com/user-attachments/assets/ccb6d074-d7b5-4eac-8a7d-b7235042ab3d" />

Start to clean up the results

<img width="1074" height="80" alt="Screenshot 2025-07-08 141217" src="https://github.com/user-attachments/assets/7f7d6e18-c899-43b4-a061-f553be4567b6" />

I will start to manipulate my search string adding and removing commands and functions to get what I like. For this alert and dashboard I will be looking for specific logon types (7 and 10). 

Logon type 7 is the compter unlocking, and Logon type 10 is a remote session into the machine.

<img width="651" height="130" alt="Screenshot 2025-07-18 144113" src="https://github.com/user-attachments/assets/3c80a728-3154-4a92-8836-a8ac54fcf042" />

<img width="1140" height="724" alt="Screenshot 2025-07-18 144123" src="https://github.com/user-attachments/assets/4933a6c1-e1ac-4f81-b62b-abe78454f837" />

<img width="1298" height="445" alt="Screenshot 2025-07-18 144145" src="https://github.com/user-attachments/assets/8e1056f8-7aa3-4343-8ccb-3b38f18f7eea" />

Next I will use the stats command to see a table of the data I am wanting to see






