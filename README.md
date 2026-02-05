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

# permission

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






