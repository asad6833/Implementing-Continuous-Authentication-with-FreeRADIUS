🔐 Implementing Continuous Authentication with FreeRADIUS
📌 Scenario

You are a security administrator for Structureality, tasked with enhancing network security by implementing continuous authentication using a FreeRADIUS server.

The goal is to ensure:

All authentication requests (including VPN) are validated
Sessions are monitored continuously
Unauthorized users are automatically disconnected
🎯 Objectives
Configure FreeRADIUS authentication server
Integrate RADIUS with VPN (RRAS)
Enforce session timeout (continuous authentication)
Validate authentication success & failure

Aligned with CompTIA SecurityX Objective 2.6 (Zero Trust Architecture)

🧪 Lab Environment
KALI VM → FreeRADIUS Server
MS10 (Windows Server 2016) → VPN (RRAS)
PC10 → VPN Client
DC10 → Domain Controller (AD, DNS, DHCP)

⚠️ Note: “No Internet Access” is expected in this lab

🛠️ Task 1: Configure FreeRADIUS Server (KALI)
Step 1: Verify FreeRADIUS Installation
freeradius -v
Step 2: Configure Users File
sudo nano /etc/freeradius/3.0/users

Add:

Jaime Cleartext-Password := "Pa55w0rd!"
Reply-Message := "Welcome, %{User-Name}"
Session-Timeout := 60
Step 3: Configure RADIUS Client
sudo nano /etc/freeradius/3.0/clients.conf

Add:

client windows {
    ipaddr = 10.1.16.0/24
    secret = Pa55w0rd!
    require_message_authenticator = no
}
📸 Configuration Screenshot
<img width="1019" height="768" alt="image" src="https://github.com/user-attachments/assets/15aa2712-3168-49a2-92bf-1e51d771985c" />

Step 4: Start FreeRADIUS in Debug Mode
sudo freeradius -X
📸 Server Ready State
<img width="852" height="727" alt="image" src="https://github.com/user-attachments/assets/97bfaca0-3ab2-4071-9adb-0beb76c872a9" />


✔️ Look for: "Ready to process requests"

Step 5: Test Authentication
radtest Jaime 'Pa55w0rd!' 127.0.0.1 0 testing123
📸 Successful Authentication
<img width="640" height="508" alt="image" src="https://github.com/user-attachments/assets/4cae0266-7f67-4be0-a8cb-2f335123730a" />

✔️ Expected:

Access-Accept
Reply: Welcome, Jaime
🖥️ Task 2: Configure VPN Server (MS10)
Step 1: Disable Windows Firewall (Lab Only)
📸 Disable Firewall
<img width="1000" height="760" alt="image" src="https://github.com/user-attachments/assets/b3bf5d4b-3f33-4249-8289-6ceb3b2ff0ce" />

📸 Firewall Fully Disabled
<img width="1018" height="760" alt="image" src="https://github.com/user-attachments/assets/abd8ab98-d83c-49f9-812a-f716857e8035" />

Step 2: Install Remote Access Role
Open Server Manager
Install:
Remote Access
DirectAccess and VPN (RAS)
Step 3: Configure RRAS

Set:

Server Type: VPN
Adapter: Ethernet 2
Authentication: RADIUS
RADIUS Server IP: 10.1.16.66
Shared Secret: Pa55w0rd!
🌐 Task 3: Configure VPN Client (PC10)
Step 1: Create VPN Connection
Connect to workplace
Server IP: 10.1.24.102
Name: RADIUS test VPN
Step 2: Configure Authentication
EAP
MSCHAPv2
🔍 Task 4: Validate Continuous Authentication
❌ Test Failed Login

Enter:

Username: Jaime
Password: Wrongpassword
📸 VPN Failure
<img width="1357" height="733" alt="image" src="https://github.com/user-attachments/assets/698e77d8-ed61-4dec-adf4-a3d3ce3bda7c" />

📸 RADIUS Reject Log
<img width="979" height="654" alt="image" src="https://github.com/user-attachments/assets/7e918456-a21e-4157-b2d8-5f3155eb57ab" />


✔️ Proof:

Authentication rejected
Access-Reject packet sent
✅ Test Successful Login

Enter:

Username: Jaime
Password: Pa55w0rd!
📸 VPN Connection Success
<img width="817" height="601" alt="image" src="https://github.com/user-attachments/assets/5130b11e-eb5a-4a5e-b5e6-aa31cf90e254" />

📸 RADIUS Accept Log
<img width="397" height="448" alt="image" src="https://github.com/user-attachments/assets/f28528e0-3218-4b33-8561-a1c94ce4dd80" />


✔️ Proof:

Access-Accept received
User authenticated successfully
⏱️ Session Timeout (Continuous Authentication)

Wait ~60 seconds

📸 Timeout & Reauthentication Trigger
<img width="1033" height="714" alt="image" src="https://github.com/user-attachments/assets/5a02ff3a-88fd-471f-b69e-d36613f13185" />


✔️ Result:

VPN disconnects automatically
Session timeout enforced
Continuous authentication confirmed
🔐 Key Security Concepts Demonstrated
✅ Zero Trust Model
✅ Continuous authentication
✅ Session enforcement (timeout)
✅ Centralized authentication via RADIUS
✅ Real-time monitoring of access
