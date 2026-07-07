This is the reference that I mainly used for this part of the project: https://galaxy.ansible.com/ui/repo/published/cisco/ios/docs/

Before I talk about the inventory and playbooks, I want to talk about the general YAML structure that Ansible uses since this is new to me.

## General YAML

Ansible uses Python and the files needed for Ansible to work are marked down using YAML, a mark-up language. How YAML works is somewhat simple and structured simliarly to Python as well:
Let's use my test_show_version.yaml for example:

```
- name: Ping test 
  hosts: routers
  tasks:
    - name: Pinging hosts
      cisco.ios.ios_command:
        commands:
          - show ip interface brief
```

I won't be explaining every playbook in great detail like this and this is something that I needed to understand so here's the explanation of each line:

`- name: Ping test` This line here is purpose of the overall task. Each `-` with no indents indicates a new whole task that's going to be written. In my *interfaces.yaml* You can see two `- name`s (Enabling GigabitEthernet2 and Creating Sub-interfaces) with no indents, indicating that they are two whole tasks being done in the playbook. 

`hosts: routers` This is referencing the *inventory.yaml* file which I will talk more about in the next section. To keep things cohesive, you would call groups that are needed in this task. Also, this is where indents start happening and needs to be two spaces, not tabs.

`tasks:` You can have a single line of an action if you want here but in my case, I wanted to organize it further and make it readable for others on what exactly is happening and also to keep the "code" consistent as well. This has to indented equal to hosts, two spaces.

`- name: Pinging hosts` Now this is going under `tasks:` so we have to break a new line and indent two spaces. Name can be whatever but the more specific, the better.

`cisco.ios.ios_command` Making sure it is indented to line up with `name` and not the `-`, this is the library and its module being called. In this case, it's Cisco's library and the IOS module calling for a command. Later, you'll see `cisco.ios.ios_config` and its the same library but a different module for configuring devices.

`commands:` New line and two spaces again. This is calling for the commands that I'm going to use, could be one, could be many. For `ios_config`, it's going to be `lines:` instead.

`- show ip interface brief` New line and two spaces. This is the actual command that I would use on Cisco devices. 

While this one doesn't have it, the *interfaces.yaml* has a `parents:` line. This line is the configuration context. Manually, this is the `interface gigabitethernet 2` or `line console 0` part of the code. If there's no `parents:`, it will apply everything in the global configuration context which might apply an unintended effect. 

## Inventory.yaml

Now, this file is where you keep all of your devices. You can separate them such as *switches.yaml*, *routers.yaml*, etc. but keeping them in one inventory file allows you to call all devices or certain groups like I mentioned above. 

```
all:
  vars:
      ansible_network_os: cisco.ios.ios
      ansible_connection: network_cli
  children:
    routers:
      hosts:
        R1:
          ansible_host: 192.168.122.71
    switches:
      hosts:
        SW1:
          #ansible_host: 
```

To quickly explain the fields and to talk two "hidden" variables, let's begin with `vars:`

`vars:` is short for variables, this is where you put anything that Ansible needs to setup before trying to connect via SSH. `network_os` is the "library" needed to talk to the devices and since it's under the `all:` grouping, it will apply to all the devices in the inventory file. `connection:` is the method of communication for Ansible. `network_cli` is simply network command line interface, the method normally used when someone SSH's to a device.

`children:` This is where you start naming different groups and logically separating devices. `routers` and `switches` are two different groups, `hosts` is formalizing the device, `SW1` and `R1` are the names of the devices and `ansible_host:` will have the FQDN or IP address of the device. 

**`#` exists for now since I'm still developing the network through Ansible. This is the commenting symbol and Ansible will not process that symbol and anything after in the same line.**

Now there are two hidden "variables" that I used to connect to the devices: 

`ansible_user:` (SSH Username) and `ansible_password:` (SSH password)

and that's on purpose. Normally in a lab setting, this is perfectly okay thing to include these two under the `all:` group or in the other groups if needed. But because I want to simulate this project as a live production level project, I learned that I could separate the variables under another folder called `group_vars` in the root directory and name it based on the group, in this case `all.yaml`.

All the file contains is:
```
ansible_user: (SSH Username)
ansible_password: (SSH Password)
```

As for the device configuration, since I have to manually do this anyway before Ansible can directly take over, make sure the SSH version is version 2. 

***Version 1 and 1.99 are not supported in my experience.***