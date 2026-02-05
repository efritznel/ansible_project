
# Lab Architecture

5 servers total

    2 × Ubuntu

    1 × CentOS

    1  × Windwos

1 Ansible Controller Node (Ubuntu)

# Initial Setup

On the Controller Node

    - Install Ansible

    - Generate SSH key pair

On Managed Nodes

    - Install OpenSSH

    - Copy controller public key:

ssh-copy-id -i ~/.ssh/key.pub <TARGET_IP>

# Inventory File

Create an inventory file on the controller and add all managed node IPs:
```yaml
[web_servers]
10.0.0.112
10.0.0.3

[db_servers]
10.0.0.38

[file_servers]
10.0.0.170

[windows]
10.0.0.211

[windows:vars]
ansible_user=ithomelabadmin
ansible_password=p@$$w0rd
ansible_port=5985
ansible_connection=winrm
ansible_winrm_transport=basic
ansible_winrm_server_cert_validation=ignore
```
# ansible.cfg Configuration

The ansible.cfg file defines default settings such as:

    - Inventory location

    - Private SSH key

    - Default remote user

This allows you to run commands without specifying parameters each time.

Instead of:
```yaml
ansible all --key-file ~/.ssh/keyname -i inventory -m ping
```

Simply run:
```yaml
ansible all -m ping
```
# Running Ad-Hoc Commands

Update package cache with sudo:
```yaml
ansible all -m apt -a "update_cache=true" --become --ask-become-pass
```
Remove Apache
```yaml
ansible-playbook --become --ask-become-pass remove_apache.yml
```

# Using Tags

List available tags:
```yaml
ansible-playbook --list-tags playbook.yml
```
Run a specific tag:
```yaml
ansible-playbook playbook.yml --tags install
```

# Privilege & Remote User Configuration

After adding user simone to the sudoers file:

SSH without password:
```yaml
ssh -i ~/.ssh/privatekey simone@<SERVER_IP>
```
Set default user in ansible.cfg:
```yaml
remote_user = simone
```
Now you can run playbooks without --become prompts:
```yaml
ansible-playbook file.yml
```
*********************************************************************************************************************
# ansible_project

- Create 4 servers, 3 with Ubuntu OS and 1 with CentOS
- 
    - Ansible is installed on the controller server
      
    - Openssh is installed on all the servers
      
    - then we have copied the public key of the controller node to all the managed nodes: ssh-copy-id -i ~/.ssh/key.pub <target_IP>
    
    - We created a file called "inventory" in Controller Node and added all the IPs of the managed Nodes

# cfg file
adding the cfg file helps to make default configuration for ansible

Instead of running this command: "ansible all --key-file ~/.ssh/keyname -i inventory -m ping" by providing key path

You can just run this: "ansible all -m ping" then it will take all the needed information in the ansible.cfg file

Commands: 

- run a command as sude user: ansible all -m apt -a update_cache=true --become --ask-become-pass

# install or uninstall apache using playbook
  - ansible-playbook --become --ask-become-pass install_apache.yml or remove_apache.yml

NB: if you have made changes in your git repository before you push from local run this: git pull --rebase origin main

# Tags
    - To see list tags available: ansible-playbook --list-tags <playbook.yml>

# Permission

    - after adding simone in sudoers file we can ssh to any of the servers without password
    
    ssh -i  ~/.ssh/privatekey simone@IPaddress

NB. we can add the user simone the ansible config file "ansible.cfg"

    - remote_user = simone
    
    - Now instead of running the playbook this way: ansible-playbook --become --ask-become-pass         file.yml
    
    - We ccan just run: ansible-playbook file.yml

# Variables & Handlers

Create a folder called "host_vars"

create a yaml for each server using IP address or DNS name

Then change the configuration in the main.yml for the specific servers group

# Manage Windows machine with Ansible
We need to set WinRM on the windows machine 
# Set up WinRM with the following command
```yaml
Enable-PSRemoting -Force
winrm quickconfig
winrm set winrm/config/service/Auth '@{Basic="true"}'
winrm set winrm/config/service '@{AllowUnencrypted="true"}'
Set-Item -Path WSMan:\localhost\service\AllowUnencrypted -Value $true
New-NetFirewallRule -Name "WinRM Port" -DisplayName "Allow WinRM" -Protocol TCP -LocalPort 5985 -Action Allow
New-NetFirewallRule -Name "WinRM Port" -DisplayName "Allow WinRM" -Protocol TCP -LocalPort 5986 -Action Allow
Restart-Service WinRM
```

# Test the connection
```yaml
ansible -i inventory windows -m win_ping
ansible -i inventory windows -m win_shell -a "hostname"
```






