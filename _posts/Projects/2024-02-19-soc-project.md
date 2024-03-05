---
title: Securing the Cloud- Leveraging SOC and Honeynet in Azure
date: 2024-02-19
categories: [Projects, Python]
tags: [Python, Azure, Security, Cloud, Administration, Incident Response]
toc: false
math: true
comments: false
mermaid: true
image: https://i.ibb.co/GnJRGpP/Main-SOC-pic.jpg

<author_id>:
    name: Iapetus J. D.
---
# SOC + Honeynet in Azure (Live Traffic) - 

Having a SOC paired with a Honeynet in Azure offers huge benefits. It helps us spot threats early, respond quickly to incidents, and gather valuable threat intelligence. With real-time monitoring and analysis, we can strengthen our security posture, meet compliance requirements, and stay ahead of evolving threats. It's like having our own security fortress in the cloud, keeping our data and systems safe around the clock.

In such a project, our goal is clear: to fortify our defenses and ensure the safety and integrity of our data and systems. By leveraging the capabilities of Azure, we can create a robust security infrastructure that meets our compliance requirements and adapts to the ever-changing threat landscape. With continuous monitoring and analysis, we can identify vulnerabilities, patch them before they can be exploited, and stay ahead of emerging threats.

This project involves constructing a mini honeynet within Azure and collecting log data from diverse sources into a Log Analytics workspace. This workspace is subsequently utilized by Microsoft Sentinel to construct attack maps, initiate alerts, and generate incidents. Initially, security metrics were assessed within the vulnerable environment over a 24-hour period. Following this, security measures were implemented to fortify the environment. Subsequently, metrics were re-evaluated for an additional 24-hour period. The ensuing section presents the results, focusing on the showcased metrics.

Ultimately, it's about creating a security fortress in the cloud—a stronghold that protects our organization from malicious actors and safeguards our most valuable assets. With a SOC and Honeynet in Azure, we have the tools and resources we need to defend against cyber threats and keep our systems safe around the clock.

In this project, the following tools were used: 

* SecurityEvent (Windows Event Logs)
* Syslog (Linux Event Logs)
* SecurityAlert (Log Analytics Alerts Triggered)
* SecurityIncident (Incidents created by Sentinel)
* AzureNetworkAnalytics_CL (Malicious Flows allowed into our honeynet)

# Architecture Before Security Controls 

<img src="https://i.ibb.co/LkZV7w5/Before-Hardening.jpg" alt="">

In a cloud environment where Network Security Groups (NSGs) are open to the entire web indiscriminately, there is essentially no filtering or restriction on incoming and outgoing traffic. This means that any traffic originating from or directed towards the internet is permitted without any form of regulation. Such a configuration leaves the entire network infrastructure highly vulnerable to various types of cyber threats, including unauthorized access, data breaches, denial-of-service (DoS) attacks, and malware infiltration.

In this scenario, all ports and protocols are accessible from any source IP address on the internet. There are no rules or policies in place within the NSGs to block or allow specific types of traffic. As a result, any entity or malicious actor with internet connectivity can potentially access the cloud resources without restriction, posing significant security risks to the organization's data and systems.

This indiscriminate openness of NSGs to the entire web disregards fundamental security principles of least privilege and defense-in-depth. It effectively eliminates any barriers between the internet and the cloud environment, exposing critical assets to exploitation and compromise. Therefore, it is crucial for organizations to implement proper network security measures, including properly configured NSGs, to mitigate these risks and safeguard their cloud infrastructure.


# Architecture After Security Controls

<img src="https://i.ibb.co/S6TsDyZ/After-Hardening.jpg" alt="">

In a cloud environment where Network Security Groups (NSGs) have security controls available, I ensure that specific rules and policies are in place to regulate traffic flow between resources within the network. These controls allow me to define and enforce access permissions based on various criteria such as source IP addresses, destination ports, and protocols. By implementing such controls, I can effectively manage access privileges, filter incoming and outgoing traffic, maintain compliance with regulatory requirements, monitor network activity for potential threats, and adapt to evolving security needs. Overall, these security controls within NSGs provide me with greater visibility, control, and protection over my cloud environment, helping to mitigate security risks and safeguard sensitive data.

The mini honeynet architecture in Azure comprises several key components:

* Virtual Network (VNet)
* Network Security Group (NSG)
* Virtual Machines (two Windows, one Linux)
* Log Analytics Workspace
* Azure Key Vault
* Azure Storage Account
* Microsoft Sentinel

In the initial setup ("BEFORE" metrics), all resources were deployed and exposed to the internet. The Virtual Machines had both their Network Security Groups and built-in firewalls configured with wide-open settings. Additionally, all other resources were deployed with public endpoints visible to the internet, rendering private endpoints unnecessary.

Following adjustments ("AFTER" metrics), the Network Security Groups were strengthened by blocking ALL traffic except for connections from my admin workstation. Furthermore, all other resources were fortified with their built-in firewalls and were additionally secured by the implementation of Private Endpoints.

# Attack Maps Before Security Controls
<img src="https://i.ibb.co/q9zqpBy/68747470733a2f2f692e696d6775722e636f6d2f317176737753582e706e67.png" alt="">
<img src="https://i.ibb.co/NKxGYGL/68747470733a2f2f692e696d6775722e636f6d2f473159675a74362e706e67.png" alt="">
<img src="https://i.ibb.co/x6Sx2QH/68747470733a2f2f692e696d6775722e636f6d2f45537239446c762e706e67.png" alt="">

# Metrics Before Security Controls
The following metrics were measured in an unsafe environment for 24 hours starting from 2024-02-16, 13:29:35 and ending 2024-02-17, 13:29:35.

| Metric      | Count       |
| :----------- | :----------- |
| SecurityEvent     | 19263       |
| Syslog   | 2099        |
| SecurityAlert | 7 |
| SecurityIncident | 278 |
| AzureNetworkAnalytics_CL | 726 |

# Attack Maps After Security Controls

*No queries were returned since there was no malicious activity in the 24 hour period after security controls were implemented.*

# Metrics After Security Controls

The following metrics were measured after security controls were implemented for 24 hours starting from 2024-02-17, 15:42:22 and ending 2024-02-18, 15:42:22.

| Metric      | Count       |
| :----------- | :----------- |
| SecurityEvent     | 8089       |
| Syslog   | 17      |
| SecurityAlert | 0  |
| SecurityIncident | 0 |
| AzureNetworkAnalytics_CL | 0 |

# Conclusion

So to wrap up, the integration of a Honeynet SOC utilizing Azure infrastructure and Microsoft Sentinel has been instrumental in enhancing my understanding of cybersecurity practices and techniques. By adhering to the National Institute of Standards and Technology (NIST) standards, particularly in terms of risk management and incident response, I've gained invaluable experience in detecting and mitigating potential threats within a controlled environment. Leveraging the advanced detection capabilities of Microsoft Sentinel, coupled with the scalability of Azure, has allowed me to effectively monitor, analyze, and respond to simulated attacks. Moving forward, I am committed to further refining my skills, staying informed about emerging threats, and continuing to apply best practices to ensure the security and resilience of my digital endeavors.