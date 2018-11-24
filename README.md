# Pre-requisite 

What image to use in AWS?
Ubuntu Server 18.04 LTS (HVM), SSD Volume Type - ami-0ac019f4fcb7cb7e6

# Ansible-AWS
Ansible with AWS

Step by Step

Step 1: 
Create EC2 instances (check Pre-requisite for selecting the image)
Instance1: controlnode 
Instance2: webserver 
Instance3: appserver 
Instance4: dbserver

Step 2:
Login to controlnode and run the following command to check release version
ubuntu@ip-172-31-82-219:~$ lsb_release -a
No LSB modules are available.
Distributor ID: Ubuntu
Description:    Ubuntu 18.04.1 LTS
Release:        18.04
Codename:       bionic

Step 3:
Assign both EC2 instances to security group called My-Web-DMZ (Allow all traffic rules for both inbound and outbound)

Step 4:
Login into controlnode and run the following commands,

   # Setup connectivity b.w. controlnode -> webserver, controlnode -> appserver, controlnode -> dbserver
    1  ssh-keygen
    2  ssh-copy-id 172.31.33.250 #ip address of your webserver, appserver & dbserver
    3  cat ~/.ssh/id_rsa.pub
    [select and copy to your clipboard]
    4 Login to webserver using putty 
    5 cat >> ~/.ssh/authorized_keys
    [paste your clipboard contents]
    [ctrl+d to exit]
    6. ssh 172.31.33.250 #ip address of your webserver, appserver & dbserver (Tip: Ensure security group allows ssh traffic b.w. all nodes)
    
    
   # Setup controlnode
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
   apt: pkg=vim state=present
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







