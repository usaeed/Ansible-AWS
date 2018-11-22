# Ansible-AWS
Ansible with AWS

Step by Step

Step 1: 
Create EC2 instance ubuntu call it controlnode

Step 2:
Create EC2 instance ubuntu call it webserver

Step 3:
Assign both EC2 instances to security group called My-Web-DMZ (Allow all traffic rules for both inbound and outbound)

Step 4:
Login into controlnode and run the following commands,

    1  apt-add-repository ppa:ansible/ansible
    2  apt-get update
    3  apt-get install ansible
    4  ansible --version
    5  ls -lha /etc/ansible/
    6  cp -R /etc/ansible myplatform
    7  cd myplatform/
    8  ls -ltr
    9  Uncomment this line inventory = hosts in the ansible.cfg file and save and exit
    10 vi hosts -> delete everything by hitting shift :1,$d -> now add the private ip of webserver -> save and exit
    11  ssh-keygen
    12  ssh-copy-id 172.31.33.250
    13  cat ~/.ssh/id_rsa.pub
    [select and copy to your clipboard]
    14 Login to webserver using putty 
    15 cat >> ~/.ssh/authorized_keys
    [paste your clipboard contents]
    [ctrl+d to exit]
    
    Weldone now you have manage to setup connectivity b.w. both EC2 instances controlnode and webserver now lets test

Step 5: 
#From controlnode run the following command 
ansible -m ping -e 'ansible_python_interpreter=/usr/bin/python3' all

172.31.33.250 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}

# Ansible-AWS Shell module
#Ansible Cheat Sheet - Run the below commands from controlnode only

ansible -m shell -a 'hostname' -e 'ansible_python_interpreter=/usr/bin/python3' all
ansible -m shell -a 'df -h' -e 'ansible_python_interpreter=/usr/bin/python3' all
ansible -m shell -a 'whoami' -e 'ansible_python_interpreter=/usr/bin/python3' all
ansible -b -m user -a 'name=testuser' -e 'ansible_python_interpreter=/usr/bin/python3' all
ansible -m shell -a 'getent passwd | grep testuser' -e 'ansible_python_interpreter=/usr/bin/python3' all
ansible -b -m user -a 'name=testuser state=absent' -e 'ansible_python_interpreter=/usr/bin/python3' all

# Ansible-AWS Roles
 mkdir roles/basic
 mkdir roles/basic/tasks
 
 vi main.yml
 - name: "Installing VIM"
   apt: pkg=vim state=installed
 [save and exit]

 cd ~/myplatform/

 vi playbook.yml
 ---
 - hosts: all
   become: true
   roles:
   - basic
 [save and exit]

#IP address of webserver (172.31.33.250)
vi ~/myplatform/hosts
172.31.33.250 ansible_python_interpreter=/usr/bin/python3
[save and exit]

# Now Test the playbook
ansible-playbook playbook.yml







