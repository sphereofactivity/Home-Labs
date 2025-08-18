# VPN Conection

## What's in this document?
This document covers the current set up and configuration of the Tailscale VPN to access the network. ]

## Using a VPN to gain access to a LAN network
Tailscale provides a unique VPN service that when used as a subnet router, allows access to a LAN by connecting clients. In this case, I've used Tailscale with my endpoint device that I actively use and on a single LXC container running Debian linux within the server. 

This allows me to access the LAN by only allowing approved devices connected to Tailscale's "tailnet" which is its own network of devices, to access the LAN server and machines within the LAN. The firewall then blocks incoming traffic and allows specific outgoing traffic to ensure secure connection to the server only. 


## Diagram of how the tailscale connection works:
![Tailscale VPN Access](/Home-Labs/VPN%20Access/img/Tailscale%20Subnet%20Router%20Map.png)

## How it works?
Access is configured by setting up a subnet router within the LAN portion of the server. Behind the firewall is the subnet 192.168.1.0/24 which holds a Debian LXC containing a Tailscale client. We then configure the Tailscale client to advertise routes to 192.168.1.0/24 and allow other Tailscale clients within the Tailnet to receive access which can then be connected to and used to reach services within the 192.168.1.0/24 subnet.

The LXC hosting the Tailscale within the LAN is connected on vmbr1 which connects to the firewall with also vmbr0 routing traffic into the server. 

Resources used:
https://tailscale.com/kb/1019/subnets

## Accomplishment
This set up allows me to gain access to everything behind the firewall from outside of the subnet and without location restriction allowing me to have remote connection to the server from any location within the GeoIP restrictions. 



