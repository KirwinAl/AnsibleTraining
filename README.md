# AnsibleTraining
This is my personal learning repository using Ansible for networking purposes and documenting them. 
This is using EVE version 6.2.0-4 Community Edition hosted on a local Ubuntu 24.04.3 multi-purpose server.

## Specs
Ryzen 7 3700X with 32GBs of 3200MHz DDR4 RAM

## Images
IOS 12.4 and vIOS L2 Switch

## Network Topology
VPC -> vIOS Switch -> C7200 Router -> Internet

<img width="521" height="667" alt="image" src="https://github.com/user-attachments/assets/0cfd936d-05b8-49db-87ca-04dd974046a1" />

A small basic network to demonstrate network automation.

This is **NOT** meant to be a beginner tutorial and my experience and tools that I used aren't the only methods to achieve and learn Ansible as my notes later will say. Please note as well that none of what I'm doing is efficient or the "correct" way of doing things, just the things that worked the best for me.

The goal of this project is to setup VLAN 10 (Management), 20 (Users) and 30 (Reserved) and the routing between them through Ansible along with changing the hostname and banner of both the router and switch. 
Once that's complete, the next step is to take another switch add it to the network through Ansible to simulate adding a new device and troubleshooting any potential problems.

If you have any inquiries, please email me at
kirwinalcan@gmail.com
