![Cloud Honeynet / SOC](https://i.imgur.com/EInzLu5.png)

<h2>Description</h2>
<p>What happens if you spin up a virtual machine and remove the firewall? Well, it turns out it will garner a lot of nefarious interest, very quickly.<br>


In this project, I constructed a honeypot using virtual machines deployed within a Microsoft Azure cloud environment. Connecting and exposing these systems to the internet, I created a mini honeynet and aggregated log data from multiple sources into a Log Analytics workspace. This log data was then funneled to Microsoft Sentinel SEIM to generate attack maps, trigger alerts, and manage security incidents. Initially, I left everything open for 24 hours to see what kind of attention a vulnerable network would attract with zero advertisements and how far these nefarious actors could get. Shortly after, I handed the network by adding security controls from the NIST 800-53 framework and then documented the changes.</p>

<h2>Languages and Utilities Used</h2>

- <b>KQL</b> 

<h2>Environments Used </h2>

- <b>Microsoft Azure</b>
- <b>Microsoft SQL server</b>
- <b>Microsoft Windows & Linux Ubuntu</b>

## Architecture Before Hardening / Security Controls
![Cloud Honeynet / SOC/ BEFORE](https://i.imgur.com/Erb9xbD.png)


I configured the firewall rules on both the Linux and Windows machines hosting the SQL server, enabling unrestricted traffic access from the Internet. These actions were integral to our monitoring efforts, as they facilitated the tracking and transmission of events to our Azure log analytics workspace. From there, they get funneled into Sentinel SEIM for us to dig into and figure out what's happening on the network.

- SecurityEvent (Windows Event Logs)
- Syslog (Linux Event Logs)
- SecurityAlert (Log Analytics Alerts Triggered)
- SecurityIncident (Incidents created by Sentinel)
- AzureNetworkAnalytics_CL (Malicious Flows allowed into our honeynet)

## Attack Maps Before Hardening / Security Controls
<img width="1222" alt="nsg-malicious-allowed-in1" src="https://github.com/mattjsnow/azure-soc-honeynet/assets/157775615/749c4594-83a4-4235-9515-83868d5d62f5">
<img width="1345" alt="linux-ssh-auth-fail1" src="https://github.com/mattjsnow/azure-soc-honeynet/assets/157775615/d04c26e0-0c2c-4a34-b0de-ec5143b20c64">
<img width="1129" alt="windows-rdp-auth-fail1" src="https://github.com/mattjsnow/azure-soc-honeynet/assets/157775615/01322ac8-d1da-499f-91e5-404f6575393f">

<p>The following table shows the metrics we measured in our insecure environment for 24 hours:<br>
  <br>
Start Time 4/4/2024, 9:37:27 PM<br>
Stop Time 4/5/2024, 9:37:27 PM</p>

| Metric                   | Count
| ------------------------ | -----
| SecurityEvent            | 14664
| Syslog                   | 11766
| SecurityAlert            | 5
| SecurityIncident         | 270
| AzureNetworkAnalytics_CL | 1654

## Architecture After Hardening / Security Controls
![Cloud Honeynet / SOC / AFTER](https://i.imgur.com/H9HCmiu.png)

The architecture of the mini honeynet in Azure consists of the following components:

- Virtual Network (VNet)
- Network Security Group (NSG)
- Virtual Machines (2 Windows, 1 Linux)
- Log Analytics Workspace
- Azure Key Vault
- Azure Storage Account
- Microsoft Sentinel

Before Hardening:
Before implementing security controls, the Network Security Groups and built-in firewalls were left wide open for all virtual machines. This lack of restriction led to numerous attempted attacks, including brute force attempts from various locations worldwide. Additionally, no private endpoints were configured, allowing unrestricted access to our systems. Despite installing an SQL server on the Windows device, it remained unaffected within the project scope, with attackers primarily targeting default credentials.

Hardening Measures Implemented:
To enhance security, several measures were implemented:

Firewall Rules and Private Endpoints: Strong firewall rules were established, and private endpoints were enabled to restrict unauthorized access and bolster network security.

Compliance Framework Adoption: NIST SP 800-53 R5 regulatory compliance was adopted to guide the hardening process. Specifically, focus was placed on implementing SC-7 Boundary Protection controls, which included subnet association and hardening entry points to the virtual network.

Detailed Hardening Steps:
Virtual networks were protected using Azure Firewall.
Subnets were associated with network security groups to control traffic flow.
Adaptive network hardening recommendations were applied to internet-facing virtual machines to further fortify defenses.
Azure AI Services resources were configured to restrict network access, reducing the attack surface.
Azure Key Vaults were configured to utilize private link, enhancing security and confidentiality of stored secrets.

## Attack Maps Before Hardening / Security Controls

```All map queries actually returned no results due to no instances of malicious activity for the 24 hour period after hardening.```

## Metrics After Hardening / Security Controls

<p>The following table shows the metrics we measured in our environment for another 24 hours, but after we have applied security controls:<br>
  <br>
Start Time 4/6/2024, 12:42:27<br>
Stop Time	4/7/2024, 12:42:27</p>

| Metric                   | Count
| ------------------------ | -----
| SecurityEvent            | 110
| Syslog                   | 344
| SecurityAlert            | 1
| SecurityIncident         | 0
| AzureNetworkAnalytics_CL | 0


| Metric                                       | % Change After Security Controls
| ---------------------------------------------| ----------------
| Security Events (Windows VMs)                | -99.25%
| Syslog (Linux VMs)	                         | -97.08%                  
| SecurityAlert (Microsoft Defender for Cloud) | -80.00%
| Security Incident (Sentinel Incidents)	     | -99.26%
| NSG Inbound Malicious Flows Allowed	         | -100.00%


## Conclusion

In conclusion, this project provided valuable insights into the immediate dangers lurking in an unprotected cloud environment. By deploying a virtual honeynet within Microsoft Azure and intentionally exposing it to the internet, we witnessed firsthand the rapid and relentless onslaught of malicious actors seeking to exploit vulnerabilities. It was very intresting to see how much attention a computer that is connected to the internet gets just from bots and scanners that are prowling the internet 24/7, scouring every nook and cranny for a way in. It's a reminder that the digital landscape is constantly under siege, and we've got to be on our toes to keep our systems safe.
