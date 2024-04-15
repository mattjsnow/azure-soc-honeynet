![Cloud Honeynet / SOC](https://i.imgur.com/EInzLu5.png)

<h2>Description</h2>
In this project, I constructed a honeypot using virtual machines deployed within an Microsoft Azure cloud environment. Connecting these systmes and exposing them to the internet, I created a mini honeynet and aggregated log data from multiple sources into a Log Analytics workspace. This log data was funneled to Microsoft Sentinel and it utilized the data to generate attack maps, trigger alerts, and manage security incidents. Initially, I monitored security metrics within the insecure environment for 24 hours. Subsequently, I implemented security controls to fortify the environment and monitored metrics for an additional 24 hours.
<br />

<h2>Languages and Utilities Used</h2>

- <b>KQL</b> 

<h2>Environments Used </h2>

- <b>Microsoft Azure</b>

## Architecture Before Hardening / Security Controls
![Cloud Honeynet / SOC/ BEFORE](https://i.imgur.com/Erb9xbD.png)


In this project, I built a small honeynet using Azure. The idea was to have a few machines open to the internet so that I could take the log data of people trying to access the systems and send into a SEIM to analyze. 

- SecurityEvent (Windows Event Logs)
- Syslog (Linux Event Logs)
- SecurityAlert (Log Analytics Alerts Triggered)
- SecurityIncident (Incidents created by Sentinel)
- AzureNetworkAnalytics_CL (Malicious Flows allowed into our honeynet)

## Attack Maps Before Hardening / Security Controls
<img width="1222" alt="nsg-malicious-allowed-in1" src="https://github.com/mattjsnow/azure-soc-honeynet/assets/157775615/749c4594-83a4-4235-9515-83868d5d62f5">
<img width="1345" alt="linux-ssh-auth-fail1" src="https://github.com/mattjsnow/azure-soc-honeynet/assets/157775615/d04c26e0-0c2c-4a34-b0de-ec5143b20c64">
<img width="1129" alt="windows-rdp-auth-fail1" src="https://github.com/mattjsnow/azure-soc-honeynet/assets/157775615/01322ac8-d1da-499f-91e5-404f6575393f">

The following table shows the metrics we measured in our insecure environment for 24 hours:
Start Time 4/4/2024, 9:37:27 PM
Stop Time 4/4/2024, 9:37:27 PM

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
- Virtual Machines (2 windows, 1 linux)
- Log Analytics Workspace
- Azure Key Vault
- Azure Storage Account
- Microsoft Sentinel

For the "BEFORE" metrics, all resources were originally deployed, exposed to the internet. The Virtual Machines had both their Network Security Groups and built-in firewalls wide open, and all other resources are deployed with public endpoints visible to the Internet; aka, no use for Private Endpoints.

For the "AFTER" metrics, Network Security Groups were hardened by blocking ALL traffic with the exception of my admin workstation, and all other resources were protected by their built-in firewalls as well as Private Endpoint

## Attack Maps Before Hardening / Security Controls

```All map queries actually returned no results due to no instances of malicious activity for the 24 hour period after hardening.```

## Metrics After Hardening / Security Controls

The following table shows the metrics we measured in our environment for another 24 hours, but after we have applied security controls:
Start Time 2023-03-18 15:37
Stop Time	2023-03-19 15:37

| Metric                   | Count
| ------------------------ | -----
| SecurityEvent            | 110
| Syslog                   | 344
| SecurityAlert            | 1
| SecurityIncident         | 0
| AzureNetworkAnalytics_CL | 0

## Conclusion

In this project, a mini honeynet was constructed in Microsoft Azure and log sources were integrated into a Log Analytics workspace. Microsoft Sentinel was employed to trigger alerts and create incidents based on the ingested logs. Additionally, metrics were measured in the insecure environment before security controls were applied, and then again after implementing security measures. It is noteworthy that the number of security events and incidents were drastically reduced after the security controls were applied, demonstrating their effectiveness.

It is worth noting that if the resources within the network were heavily utilized by regular users, it is likely that more security events and alerts may have been generated within the 24-hour period following the implementation of the security controls.
