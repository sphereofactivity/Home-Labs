# Firewall Configuration

## What's in this document?
This document covers the current set up and configuration of the lab's firewall. The firewall of choice in this use case is "Opnsense". I've picked this firewall option for its open source resources and thriving community to develop custom configurations that suite the needs of the network.

## Default Password Changes and root user disabled.
The initial step to hardening the firewall is changing the default password to the root user. I've then also created two user groups; the first is a root level group with only the root user sitting ina deactivated state. The second is a Super Admin group with only my user profile within it to action changes to the firewall.

## Geolocation IP Restrictions
The next step was to restrict incoming connections based on Geolocation. I used a Maxmind API key to download a database of Geolocation Ips tp restrict. I then used OPNsense's Geoip settings to provide the range of ips and created Firewall Aliases to with individual entries to specify each local area for possible ingress connections. The following locations were chosen to be restricted based on ensuring a secure posture:
- North Korea
- Africa
- Asia (China & India)
- Middle East

## SSH Disabling
This setting is determinant in how the administration of the network will take place. Securing the firewall with SSH disabled is more secure but also requires you to physically be connect to to the firewall to administer changes. I have chosen to disable SSH connections as this is a home network that only needs to be accessed via my in-unit devices.

## Backups
Reliability and availability means consistent backing up and version control, and also automating that backup so I don't need to manually hand back up each change. To accomplish this, we're using Git with Opnsense's XML backup config file that will automatically backup to a private git repo. 

To accomplish this, we have a PKI system with the private key provided to OPNsense and the public key provided to ONLY the repo in use. This reduces our surface of exposure so that the keys are only paired on the repo itself and not my overall github account.

We are also running a cron job to run every 28th of the month to push a backup config XML file to the github repository. This is to ensure that there are extensive backup versions regardless of a backup initiating when a config changes occurs providing version control choice and ensuring availability.


# Firewall Rules

## Interface Grouping
Since this is a home lab and in its current configuration, all traffic routes to the firewall, then the proxmox gateway IP, I've created three interface groups to govern connections to start but we're not applying these groups for the WAN interface because it doesn't use a "reply-to" directive. The current network overview can be viewed as below for the NICs.

| Interface    | Network |
| -------- | ------- |
| Vmbr0  |  Proxmox WAN  |
| Vmbr1 | Local LAN     |

Within OPNsense, we have the following groups associated to each interface:

| Group    | Network |
| -------- | ------- |
| ALL_LOCAL  | Vmbr1, all local interfaces    |
| WAN | Vmbr0     |
| LAN| Vmbr1 |

We can now use the groupings to apply policies directly to the group without needing to manually assign it to each interface.

## Aliases
This is the grouping chart I'll be using to manage the ports on firewall.

| Name    | Description | Content|
| -------- | ------- | ------- |  
| PORTS_OUT_LAN | Ports allowed for intranet | 21, 22, 161, 80, 8080,443, 8443, 8880, 10001, 5001, 623, 5900, 3389, 49152:65535|
| PORTS_OUT_WAN | Ports allowed for internet     |21, 22, 80, 8080, 443, 8443, 465, 587, 993, 49152:65535 |
| PORTS_ANTI_LOCKOUT| OPNSense admin ports | 22, 443 |

Content Ports Summarized:
- 53 DNS
- 5353:5354 mDNS
- 123 NTP
- 21 FTP
- 22 SSH
- 161 SNMP
- 80 HTTP
- 8080: HTTP alt / UniFi device and application communication
- 443 HTTPS
- 8443 HTTPS alt / UniFi application GUI/API as seen in a web browser
- 8880 UniFi HTTP portal redirection
- 10001 UniFi device discovery
- 465 SMTPS
- 587 SMTPS
- 933 IMAPS
- 5001 iPerf
- 623 IPMI
- 5900 VNC
- 3389 RDP
- 49152:65535 ephemeral ports

## Policies added

### Reject Intranet Traffic by default
By default and for security, I've blocked intranet traffic by default but allow this rule to be overriden should it be needed and have logging set up to allow list ports should the need be present too.

### Allow ICMP pings but have this rule to disable it when needed
I've allowed ICMP pings for the entire local network for troubleshooting. Pings are maliciously exploitable so I've decided to add this rule so we can change it to disabled quickly, should the need be present.

### 

![icmp rule](/Firewall/img/all_local_ICMPping_src.png)


### Under Construction...












