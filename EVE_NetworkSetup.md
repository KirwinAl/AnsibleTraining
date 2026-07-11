Before I talk about the network itself, I would like to talk about the complications of the EVE-NG setup...

1) The setup is now different, hence why I renamed the EVE-NG_Notes Old.
    - While the image setup is the same, the image of the router changed. It is NOT C7200 image anymore but now it is a CSR1000 IOS XE v17 image. 
    A quick rundown of the installation (Link here: https://www.eve-ng.net/index.php/documentation/howtos/howto-add-cisco-csrv1000-16-x-denali-everest-fuji/)*:
        - Uploaded the image to the images_temp folder and renamed to virtioa.qcow2
        - Created a directory in the qemu folder of the image: mkdir /opt/unetlab/addons/qemu/csr1000vng-universal9.17.03.05
        - Renamed the image file to virtioa.qcow2 (in File Explorer) and moved it to that new directory: mv virtioa.qcow2 /opt/unetlab/addons/qemu/csr1000vng-universal9.17.03.05
        - Fixed the permissions: /opt/unetlab/wrappers/unl_wrapper -a fixpermissions
        
        *Skipped the image creation since it was pre-made for me

**Why?**

While the image worked great, there was an issue when trying to manually SSH to the router using my macOS. To give a rough explanation, the C7200 uses old algorithms for SSH that my macOS could not support. I tried using different SSH methods of OpenSSH and paramiko but these newer methods have old algorithms disabled by default. I was able to eventually find a work-around for manually connecting but with Ansible, it was a hard-no. I researched and saw Reddit's recommendation for ENCOR for CSR1000V and it works great. 

2) The environment setup is entirely different:

Ansible is only configured on the server and I switched to a DevOps approach now. Git is now the main method of uploading and managing playbooks and inventories.

**Why?**

With the macOS to Ubuntu setup, I ran into one really annoying problem. To give context, this is the actual layout of the environment:

*macOS -> Ubuntu -> VM within Ubuntu*

and because of the virtualization aspect, things got complicated. I could manually SSH and remote around freely within the virtual network but once I tried using Ansible using the test playbook (all it does is gather the version of the router that is there), this is where I exhausted everything. I researched the bastion host extensively and came to the conclusion: If I want this to work with minimal headaches, I need to directly use Ansible on the host machine instead of trying to remotely solve everything...
And that's where I arrived to this configuration: Work on playbooks on either macOS or Windows, push to Git, pull on the Ubuntu server and run the Ansible playbook via SSH or through RDP. This way it is still remote but now has an extra step instead of working on the virtual devices directly.

## Project Goals

Now, this file is going to layout some goals of the project in a bit more details like IP addresses, commands that would manually achieve the configuration wanted.

My private network inside EVE is going to have an IP address of 10.10.27.0/24 and the goals of the playbooks are as follows:

1. Ansible must create VLANs 20 and 30 on SW1. Must name them correctly (see ReadMe) and assign ~~GigabitEthernet 0/1 and 1/1 to VLAN 10~~, Gi 0/2-1/2 to VLAN 20 and Gi 0/3-1/3 to VLAN 30. Ansible should also disable 1/0 as it won't be used. ~~**VLAN 10 is pre-made so we can SSH on the switch with zero issues. A loopback network would also achieve this**~~ 

**I will talk about why I removed VLAN 10 in NetworkNotes.md**

2. Ansible should create sub-interfaces on the router's GigabitEthernet2 with the associated VLANs. I will add OSPF routing as well so there is communication between all networks.
3. Each VLAN is going to have 50 hosts each so subnetting to /26 network is a must. Each VLAN interface should have the second usable address while the first goes to the sub-interfaces on the router.
4. The Router should handle the DHCP pool of each subnet, gateway being the router itself.

That's it for now. The playbook for now is something short and sweet to demonstrate that I have something working and viable for real-work rollout. I do plan on adding more complicated things like device expansion, etc. and there's going to be notes for when I'm making Ansible playbooks once I get the virtual network going.
