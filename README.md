# Wazuh Agent Project

This project was created in order to demonstrate how Wazuh can act as a SIEM (Security Information and Event Management) tool by downloading the tool in Linux (manager) and then deploying an agent in Windows (host machine).

<img src="https://img.shields.io/badge/virtualbox-%23183A61.svg?style=for-the-badge&logo=virtualbox&logoColor=white" />
<img src="https://img.shields.io/badge/Ubuntu-E95420?style=for-the-badge&logo=ubuntu&logoColor=white" />
<img src="https://img.shields.io/badge/Windows-0078D6?style=for-the-badge&logo=windows&logoColor=white" />

## Prerequisites

- Oracle Virtualbox installed
- Ubuntu Server 20.04+ installed in Virtualbox
- Windows Host 10+ installed in Virtualbox
- Custom NAT network between Ubuntu and Windows for intercommunication


## Installing and Accessing the Wazuh Manager in Ubuntu

1. Add Wazuh GPG key via terminal window
```bash
curl -s https://packages.wazuh.com/key/GPG-KEY-WAZUH | sudo gpg --dearmor -o
/usr/share/keyrings/wazuh-archive-keyring.gpg
```
This adds the GPG key to verify packages.

2. Download and Execute Wazuh installation script
```bash
curl -sO https://packages.wazuh.com/4.14/wazuh-install.sh && sudo bash
./wazuh-install.sh -a -i
```
With the help of this script, all required services are installed and configured automatically (the interface port is usually 443). The login credentials for the dashboard are provided at the end of the installation.

3. Accessing the dashboard
Open a browser in the Ubuntu server and go to:
**hxxps[://]ubuntu-vm-ip**
- Accept any browser security warning due to the self-signed certificate
- Log in using the credentials that were provided

## Installing the Wazuh Agent for the Windows Host

1. Download the latest Wazuh agent MSI installer from the official documentation:
<a href="https://documentation.wazuh.com/current/installation-guide/wazuh-agent/wazuh-agent-package-windows.html"> Windows endpoint MSI installer</a>

2. Install the MSI package using default settings

## Register the Agent with the Manager

1. Run the agent management utility via terminal window:
```bash
sudo /var/ossec/bin/manage_agents
```
- Select A to add an agent
- Assign a name to the agent
- Register the IP address of the Windows host
- After adding the agent, select E to generate and extract the agent key

2. Apply the key in the Windows agent
- Open Wazuh Agent Manager GUI ("Manage Agent") from the Start Menu
- Paste the copied agent key in the **Authentication key** field
- Add the IP address of the Ubuntu manager in the **Manager IP** field
- Save and restart the agent service
The agent will then be onboarded in the Wazuh dashboard

## File Integrity Monitoring on Windows

Wazuh supports real-time monitoring of file and folder changes using Syscheck. This allows a good example to demonstrate how file integrity is monitored in Wazuh and what kind of alerts and logs are generated from file modification, deletion etc.

1. Edit Agent Configuration
- Open the following configuration file:
```bash
C:\Program Files (x86)\ossec-agent\ossec.conf
```
- Add the following entry inside the Directory block:
```bash
<directories realtime="yes">C:\Users\Example\Example</directories>
```
This allows for real-time monitoring of the specified folder. Multiple other entries for various files can be added as well

2. Restart the agent to apply the configuration

## Verifying Setup

After ensuring that the Windows agent is listed and is active, proceed to perform actions in the monitored folder in the Windows agent. Confirm that alerts appear on the dashboard. With the actions having been made, go to the **Integrity Monitoring** section in the Wazuh dashboard of the Ubuntu manager to verify that alerts have been generated.

## Conclusion

This basic Wazuh setup between two virtual machines can help in learning and experimentation with various types of log monitoring, a fine example being file integrity monitoring.
