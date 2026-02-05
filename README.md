
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
# Variables & Handlers

Host Variables

Create a directory:
```yaml
host_vars/
```

Inside it, create YAML files per server:
```yaml
host_vars/10.0.0.112.yml
host_vars/10.0.0.3.yml
```

Define server-specific values such as:
```yaml
apache_port: 8080
env: staging
```
Use them in playbooks via {{ variable_name }}.

# Managing Windows Servers with Ansible

Ansible communicates with Windows using WinRM.

Enable WinRM on Windows (PowerShell as Admin)
```yaml
Enable-PSRemoting -Force
winrm quickconfig
winrm set winrm/config/service/Auth '@{Basic="true"}'
winrm set winrm/config/service '@{AllowUnencrypted="true"}'
Set-Item WSMan:\localhost\service\AllowUnencrypted -Value $true

New-NetFirewallRule -Name "WinRM_HTTP" -Protocol TCP -LocalPort 5985 -Action Allow
New-NetFirewallRule -Name "WinRM_HTTPS" -Protocol TCP -LocalPort 5986 -Action Allow

Restart-Service WinRM
```

# Testing Windows Connectivity
```yaml
ansible -i inventory windows -m win_ping
ansible -i inventory windows -m win_shell -a "hostname"
```
