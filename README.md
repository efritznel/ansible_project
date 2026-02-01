# ansible_project
This is a new project

# cfg file
adding the cfg file helps to make default configuration for ansible

Instead of running this command: "ansible all --key-file ~/.ssh/keyname -i inventory -m ping" by providing key path

You can just run this: "ansible all -m ping" then it will take all the needed information in the ansible.cfg file

Commands: 

- run a command as sude user: ansible all -m apt -a update_cache=true --become --ask-become-pass
