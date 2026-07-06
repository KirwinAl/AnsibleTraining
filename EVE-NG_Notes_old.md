Creating Virtual labs of Cisco routers, switches, and hosts for Ansible lab practice 
--------------------------------------------------------------------------------------
Creating the VM within Linux:
`sudo virt-install --name eve-ng --ram 8192 --vcpus 6 --disk path=/var/lib/libvirt/images/eve-ng.qcow2,size=80 --cdrom /var/lib/libvirt/images/eve-ce-prod-6.2.0-4-full.iso --os-variant ubuntu22.04 --network network=default --graphics vnc,listen=0.0.0.0 --cpu host-passthrough`
Typically, I would use something like VMWare Workstation on a Windows PC but I took the chance of using a physical Linux server due to its low OS processing load and memory usage compared to Windows. Using RedHat’s blog about virsh: https://www.redhat.com/en/blog/virsh-subcommands , I was able to identify the main couple of commands that I would be using for this personal project:
`virsh start/shutdown/reboot`
While it’s not important for me in this project, it’s good to note that: `virsh list --all`, `virsh domifaddr <vm name>` are good commands to write down (lists all VMs and their state and gets the IP address of the VM, respectively).

~~Currently, my layout is this: MacBook or Windows PC (Using WSL) -> SSH into Linux Server -> EVE-NG hosting the images. While it’s a bit impractical from a lab standpoint (because I can and should have been running it from my main PC to begin with), this allows me to practice as a remote dev instead of a physical one. This also allows the process to be platform-independent, meaning, no matter how you want to start this project (Mac, Windows or LinuxOS)~~

~~Interestingly, this layout allows me to learn some SSH commands that can be useful later~~
~~For example:~~
~~ssh -J user@server-ip eve@vm-ip~~
~~Allows me to connect to the server and *jump* to the EVE VM directly~~
~~-L was another flag I could use if I wanted to tunnel to the server and access the web UI on my main machines~~

**Instead of deleting this section, I mentioned the new setup and to record a bit of my failures/complications for this project as well. Please see EVE_NetworkSetup.md to be up-to-date.**

Loading Images on EVE-NG
------------------------
Normally, this is a process that doesn't normally happen as a Network Admin/Engineer. Therefore, I'm treating this process as receiving functional devices but with corrupted images. 

Router
-------
~~The process starts similarly for both but starting with the one that gave me success~~
~~Following this guide from EVE-NG: https://www.eve-ng.net/index.php/documentation/howtos/howto-add-cisco-dynamips-images-cisco-ios/~~

~~After obtaining the image (In my case, the IOS 12.4 from the C7200 Router), I SSH'ed to the EVE VM and followed these steps:~~
~~1- Created a temporary directory: mkdir images_temp~~
~~2- Moved the .image to the correct addons folder: mv c7200.image /opt/unetlab/addons/dynamips/~~
~~3- Fixed the permissions: /opt/unetlab/wrappers/unl_wrapper -a fixpermissions~~

~~The guide called for deleting and cleaning up the temporary but I left it for future devices that I may want to experiment with.~~

~~After moving the image, I moved into the web UI and logged in and started a lab to play with. I added a router node with the template of Cisco Dynamips IOS (The image that I have and put in). After starting the node, everything booted and everything worked!~~

**Read EVE_NetworkSetup.md to get the new installation method I used.**

Switch
-------
This process gave me the most issues. Originally, I was loading with switch Linux images since they're designed for simulations. Since it did not work, I won't go into details on setting up IOL images but after researching, I have also discovered that vIOS images for switches exists! But I will say the error that I kept running into while setting up IOL, after creating my iourc file, the switch will have a fatal boot as there was a missing file and it kept failing POST.

The process that did work is very similar to the router setup but with one specific distinction since it's now on qcow2 instead of a bin with IOL:
The folder that holds the .qcow2 image MUST have a specific folder name and file name. Here are the steps I did, followed from https://www.eve-ng.net/index.php/documentation/howtos/howto-add-cisco-vios-from-virl/:

1- Created a directory in the qemu folder of the image: mkdir /opt/unetlab/addons/qemu/viosl2-adventerprisek9-m.SSA.high_iron_20200929
2- Renamed the image file to virtioa.qcow2 (in File Explorer) and moved it to that new directory: mv virtioa.qcow2 /opt/unetlab/addons/qemu/viosl2-adventerprisek9-m.SSA.high_iron_20200929
3- Fixed the permissions: /opt/unetlab/wrappers/unl_wrapper -a fixpermissions

Tested a switch node and everything worked great!

This ends the setup for EVE-NG and now I will start the Ansible section of this project.
