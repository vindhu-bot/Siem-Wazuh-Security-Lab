# SIEM-Wazuh-Security-Lab
Wazuh SIEM lab includes: Custom PowerShell detection, running Atomic Red Team simulations, Windows authentication monitoring, and Kali Linux for testing and investigation purposes.

## LAB Overview 

This project demonstrates hands on experience in terms of security and monitoring developed with Wazuh running on Ubuntu server, a monitored Windows Server 2022 endpoint with Sysmon installed, Atomic Red Team to run controlled attack simulations, and Kali Linux for security testing purposes.

## The lab showcased three different security scenarios: 
1. **Atomic Red Team & Custom PowerShell Detection Rule** - Ran PowerShell activity and developed a custom Wazuh detection rule based on that with Sysmon telemetry. 

2. **Windows Authentication Monitoring** - Analyzed successful(ID 4624) and failed(ID 4625) Window logins and developed a visualization displaying authentication activity.

3. **Kali Linux Security Testing/Investigation** - Performed network reconnaissance against Windows VM and performed a failed RDP authentication attempt, and then investigated the event through Wazuh.

## Lab Architecture
<img width="736" height="565" alt="SIEMDIA" src="https://github.com/user-attachments/assets/5173a034-97fd-425d-a511-6738f47c86bd" />

## Lab Components 
### Virtual Machine 1: Ubuntu server-Wazuh SIEM
- **Wazuh Manager** is the SIEM component responsible for receiving and analyzing the security telemetry from the endpoint being monitored(In this case the Windows Server 2022 VM)
- Responsible for collecting data from endpoints and it decodes and matches them against any threat rules that track suspicious activity, vulnerabilities, and any form of anomalies.
- Essentially the "**Central Control Tower**"

### Virtual Machine 2: Windows Server 2022(Endpoint being monitored)
- **Wazuh Agent** - Collects security telemetry from the Windows Server and sends it to the Wazuh Manager.
- 








## Scenerio 1: Atomic Red Team & Custom PowerShell Detection Rule

### Objective: 
Recreate suspicious PowerShell execution through Atomic Red Team on Windows Server 2022 VM 

