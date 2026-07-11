This section covers networking notes specific to this project; moments where configurations work on physical hardware but behave differently in a virtual environment. 

## Why is there no VLAN 10?

To give context, I have to talk about the setup that I'm doing here: ***Router-on-a-stick***.

Router-on-a-stick, or InterVLAN routing as it can be called, works like this:
![Switch connecting 3 VLAN connections](image-1.png)

A network has a router and a switch with 3 VLANs, while this is a valid configuration for separating traffic between VLANs, this is incredibly inefficient. 

![Demonstration for trunking](image-2.png)

On the switch, you can **trunk** the connection between the router and switch to have all VLAN related communication go through the same port instead of **3** ports to do the same thing. 

And that's how my network currently works but there's an issue related to EVE-NG... You can configure VLAN 10, 20, 30 with their respective sub-interfaces on the router just fine on real hardware but virtually, there is a quirk that doesn't allow that. I must have VLAN 1 configured otherwise the router and switch cannot communicate. I have no idea if that's something that can be fixed but this is the approach I'm taking to finish this project.

##
As of 7/11, there is no VLAN playbook as I have to think of how to connect via SSH manually before I make the playbook (that's how I'm approaching Ansible: do it manually first and then automate it). I'm thinking that I have to do bastion jump host again but I will make a new section in the AnsibleNotes.md called 'VLAN Playbook' detailing the switch configurations. Once the whole network is communicating, I will make a DHCP pool playbook then a banner playbook to finish things off. When everything is completed, we will create a new network with the same playbooks and demonstrate with a video the capabilities of Ansible. 