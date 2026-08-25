# SIEM-Wazuh-Security-Lab
Wazuh SIEM lab includes: Custom PowerShell detection, running Atomic Red Team simulations, Windows authentication monitoring, and Kali Linux for testing and investigation purposes.

## LAB Overview 

This project demonstrates hands on experience in terms of security and monitoring developed with Wazuh running on Ubuntu server, a monitored Windows Server 2022 endpoint with Sysmon installed, Atomic Red Team to run controlled attack simulations, and Kali Linux for security testing purposes.






## The lab showcased three different security scenarios: 
1. **Atomic Red Team & Custom PowerShell Detection Rule** - Ran PowerShell activity and developed a custom Wazuh detection rule based on that with Sysmon telemetry. 

2. **Windows Authentication Monitoring** - Analyzed successful(ID 4624) and failed(ID 4625) Window logins and developed a visualization displaying authentication activity.

3. **Kali Linux Security Testing/Investigation** - Performed network reconnaissance against Windows VM and performed a failed RDP authentication attempt, and then investigated the event through Wazuh.
&nbsp;&nbsp;&nbsp;&nbsp;
## Lab Architecture
<img width="736" height="565" alt="SIEMDIA" src="https://github.com/user-attachments/assets/5173a034-97fd-425d-a511-6738f47c86bd" />





&nbsp;&nbsp;&nbsp;&nbsp;







## Lab Components --> *VMs were deployed on a self-hosted, bare-metal(Esxi) home server
### Virtual Machine 1: Ubuntu server - Wazuh SIEM
- **Wazuh Manager** is the SIEM component responsible for receiving and analyzing the security telemetry from the endpoint being monitored(In this case the Windows Server 2022 VM)
- Responsible for recieving data from endpoints and decoding/matching them against any threat rules that track suspicious activity, vulnerabilities, and any form of anomalies.
- Essentially the "**Central Control Tower**"

### Virtual Machine 2: Windows Server 2022(Endpoint being monitored)
- **Wazuh Agent** - Collects security telemetry from the Windows Server and sends it to the Wazuh Manager.
- **Atomic Red Team** - Library of focused security tests to recreate real-world attacker behaviors(allows us to verify whether monitoring tools pickup on this behavior)
- **Sysmon** - Gives detailed Windows system telemetry, eg. process creation/cmd line activity

### Virtual Machine 3: Kali Linux - Security Testing
- **Nmap** - Used for Windows reconnaissance and searching for exposed services on Windows Server
- **FreeRDP** - Used to perform a controlled RDP authentication attempt against the Windows Server





&nbsp;&nbsp;&nbsp;&nbsp;





## Lab Setup and Configuration
 ### 1. Install Wazuh on Ubuntu Server
-  I installed the Wazuh SIEM environment on the Ubuntu Server VM. The Wazuh Manager lives here & it's responsible for being the central analysis component.
-   It receives security telemetry from the endpoints connected to it and evaluates events based on the detection rules. 
 
<img width="498" height="199" alt="Wazuh Install" src="https://github.com/user-attachments/assets/4236c2ea-67bd-482f-a865-8706a5442009" />
 
 Fig 1. Successful deployment of the Wazuh SIEM environment on Ubuntu Server.

### 2. Windows Endpoint Connection
- I installed the Wazuh agent on the Windows Server 2022 endpoint.
- After connecting the agent to Wazuh Manager, I had to confirm the Windows endpoint agent was actually reporting to the Wazuh manager(Ubuntu Linux Server VM).

<img width="491" height="280" alt="Wazuh 2" src="https://github.com/user-attachments/assets/678bda04-dffa-413a-b3c1-63c499ffcff0" />

<img width="1247" height="506" alt="Wazuh 3" src="https://github.com/user-attachments/assets/d6c7c3a2-a0b5-4728-933c-599704d6f7cb" />enerio 

Fig 2. Windows Server 2022 endpoint connected successfully and reports as active to the Wazuh Manager. Name of endpoint is "watcher-goat"

### 3. Implement Sysmon on Windows Server 2022
- I installed Sysmon on the Windows Server 2022 endpoint in order to provide detailed endpoint telemetry.
- Once the installation was complete I made sure the Sysmon was generating events inside the Microsoft Windows Sysmon Operational event log.

<img width="1003" height="567" alt="Wazuh 4" src="https://github.com/user-attachments/assets/7cb064b4-1aeb-4c5f-9b3e-303db67de8c8" />

Fig 3. Sysmon successfully generated endpoint telemetry in the Windows Sysmon Operational event log!


### 4. Integrate Sysmon with the Wazuh Agent
- I configured the Wazuh "**ossec.conf**" file to monitor the "**Microsoft-Windows-Sysmon/Operational**" event channel.
- This basically lets the Wazuh agent collect the Sysmon telemetry from Windows Server endpoint and send it to the Wazuh Manager on the Ubuntu Server VM to analyze. 

<img width="1117" height="623" alt="Wazuh 5" src="https://github.com/user-attachments/assets/030d588f-4396-44bd-aea9-c7f4881a8fdb" />

Fig 4. This displays the Wazuh Agent configuration we pasted into the **ossec.conf** file to collect the Sysmon Operational channel telemetry from Windows endpoint




&nbsp; &nbsp; &nbsp;





## Scenerio 1: Atomic Red Team & Custom PowerShell Detection Rule

### Objective: 
Simulate suspicious PowerShell activity through Atomic Red Team on Windows Server 2022 VM endpoint, collect the activity via Sysmon, and develop a custom Wazuh detection rule to identify the behavior. 

### Steps:
<img width="635" height="535" alt="SIEMDIA2" src="https://github.com/user-attachments/assets/e7b3bff2-9065-4868-a0c6-2292ede706e8" />

### Atomic Red Team Installation(prep)
Atomic Red Team was installed on the Windows Server 2022 endpoint to safely recreate attacker techniques in a controlled lab environment. Then I verified to make sure Atomic Red Team library was successfully installed and ready before actually executing the PowerShell simulation.

<img width="1059" height="278" alt="Wazuh 6" src="https://github.com/user-attachments/assets/6e86dbc7-2248-44a9-a804-8be451b797d9" />

Fig 1. Atomic Red Team successfully installed and confirmed on the Windows Server 2022 endpoint, the project **can begin now!**

&nbsp; &nbsp;

### Step 1:  Atomic Red Team: Encoded PowerShell Execution

- I used Atomic Red team in order to simulate the suspicious PowerShell activity on the Windows endpoint.
- Specifically I selected Atomic Test 17 under the MITRE ATT&CK technique T1059.001 & this attack showed the encoded PowerShell command. 

<img width="527" height="288" alt="Wazuh 7" src="https://github.com/user-attachments/assets/77e224df-4b61-4b7b-91fe-e34f36bc8f07" />

Fig 2. ^ Displayed the MITRE ATT&CK simulations available 

<img width="915" height="487" alt="Wazuh 9" src="https://github.com/user-attachments/assets/0369c7b4-29c7-413c-9ee7-a7fb941f6474" />

Fig 3. ^ Displayed the details for the MITRE ATT&CK 17 showing the T1059.001 PowerShell technique and the obfuscated PowerShell command which was used during the execution. 

So the clues are...
- Technique: PowerShell
- Program: powershell.exe
- Argument: -e
- Content: Long encoded command 

<img width="590" height="388" alt="Wazuh 8" src="https://github.com/user-attachments/assets/4dac9e83-f168-4fd0-a004-c037decb8ac0" />

Fig 4. ^ Ran Atomic Red Team test 17 for MITRE ATT&CK T1059.001, generating controlled PowerShell activity on the Windows Server 2022 endpoint.



### Step 2: Sysmon Event ID 1-Process Creation

- After executing Atomic Test 17, Sysmon captured the PowerShell process creation as "Event ID 1" on the Windows Server endpoint. The event was sent to Wazuh manager(Ubuntu Server) to be analyzed(we will go into more detail next step).

<img width="398" height="392" alt="Wazuh 10" src="https://github.com/user-attachments/assets/d228d4a1-307c-4f9d-8e6b-c9bd00371398" />

 Fig 5. Wazuh displaying Sysmon Event ID 1(Process Creation) for PowerShell activity captured from the Windows Server 2022 endpoint. 

 - ^ Wazuh analyzed the activity and mapped the PowerShell behavior to the MITRE ATT&CK T1059.001(PowerShell) under the Execution tactic

<img width="296" height="58" alt="Wazuh 11" src="https://github.com/user-attachments/assets/1372ed85-4b47-4701-8d34-b7baa9dc078e" />

Fig. 6. ^ Wazuh mapping the detected PowerShell activity to MITRE ATT&CK T1058.001(PowerShell) under the Execution tactic. 



### Step 3: Wazuh Agent --> Wazuh Manager 
- The Wazuh Agent on the Windows Server 2022 collected that Sysmon telemetry and sent it to the Wazuh Manager on the Ubuntu Server to be analyzed.
- The Wazuh received the encoded PowerShell command and the source agent information, letting the information be investigated centrally.

<img width="647" height="368" alt="Wazuh 13" src="https://github.com/user-attachments/assets/58513d4e-85f6-4fe3-b195-f3ab1ba7584f" />

Fig 7. ^ Wazuh displaying the encoded Powershell telemetry, which has been collected from the Windows Server 2022 agent(watcher-goat)



### Step 4: Custom Wazuh Detection Rule

- Wazuh already provides detection for PowerShell activity, so I developed my own custom rule "100101" to focus on specific behavior.
  
- This custom rule looks for process creation for **powershell.exe -e** which means PowerShell is running an encoded command that isn't immediately readable in plaintext.

- When this pattern is detected, the rule generates a Level 10 alert and maps the activity to MITRE ATT&CK T1059.001(PowerShell). This rule shows how **custom detection logic can be used to prioritize specific suspicious behavior**

  <img width="482" height="152" alt="Wazuh 14" src="https://github.com/user-attachments/assets/882052ac-b184-4364-99cb-1dbe921cf197" />

Fig 8. ^ Custom Wazuh rule 100101 configured to detect **powershell.exe -e** and generate a level 10 alert & map activity to MITRE ATT&CK T1059.001. 


### Step 5: Custom rule triggered

- After running the Atomic Red Team PowerShell test, the activity matched the cutom rule 100101.
  
- As expected, Wazuh generated the level 10 alert which confirmed that the custom detection rule successfully identified the encoded PowerShell behavior.

<img width="393" height="233" alt="Wazuh 15" src="https://github.com/user-attachments/assets/d82a92ea-1665-4f9d-ba80-e5ce54d6d4f6" />

Fig 9. ^ Atomic Red Team Test 17 was executed on Windows Server 2022 endpoint triggering encoded PowerShell detection.

<img width="591" height="236" alt="Wazuh 16" src="https://github.com/user-attachments/assets/e7619e91-3fca-4231-82e3-12eadd3a7340" />

Fig 10. ^ Wazuh Level 10 alert confirmed the custom rule **100101** detecting PowerShell activity and mapped it to the MITRE ATT&CK.

RESULT: The simulation successfully detected the endpoint telemetry and the custom rule we made **100101** generated that level 10 alert!




## Scenario 2: Windows Authentication Monitoring

### Objective 

Monitor Windows authentication activity in Wazuh and build a visualization comparing successful and failed login attempts  

### Step 1: Verify Windows Authentication Events 
- Successful logins are recorded as **Event ID 4624** and failed logins as **Event ID 4625**
- I made sure both authentication events were being collected from the Windows Server 2022 endpoint in Wazuh

<img width="370" height="201" alt="Wazuh 16" src="https://github.com/user-attachments/assets/6c84fad4-6f01-489c-9310-cef2c9f079cd" />

Fig 1. ^ Wazuh authentication attempt displaying Event ID 2624 which indicates a successful login attempt

<img width="338" height="211" alt="Wazuh 17" src="https://github.com/user-attachments/assets/ae7fa42d-b1e1-4aec-b6d7-ef83ad048617" />

Fig 2. ^ Wazuh authentication attempt displaying Event ID 2625 which indicates a failed login attempt


### Step 2: Build Authentication Visualization

- I developed the Wazuh visualization using **Event ID 4624** & **Event ID 4625** in order to compare successful vs failed logins.

- This makes it easy to visualize and monitor unusual patterns such as repeated failed login attempts

<img width="326" height="209" alt="Wazuh 18" src="https://github.com/user-attachments/assets/f35f39c3-9517-480f-a58b-d6723db23226" />

### Result:
Successfully collected and analyzed Windows authentication events and developed a Wazuh visual comparing failed and successful logins.












