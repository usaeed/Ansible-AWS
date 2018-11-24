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

   # Setup connectivity b.w. controlnode -> webserver (repease step 1 to 5 with all nodes e.g. controlnode -> node1, controlnode -> node2, controlnode-> node3) 
    1  ssh-keygen
    2  ssh-copy-id 172.31.33.250
    3  cat ~/.ssh/id_rsa.pub
    [select and copy to your clipboard]
    4 Login to webserver using putty 
    5 cat >> ~/.ssh/authorized_keys
    [paste your clipboard contents]
    [ctrl+d to exit]
    
   # Setup controlnode
    6  apt-add-repository ppa:ansible/ansible
    7  apt-get update
    8  apt-get install ansible
    9  ansible --version
    10  ls -lha /etc/ansible/
    11  cp -R /etc/ansible myplatform
    12  cd myplatform/
    13  ls -ltr
    14  Uncomment this line inventory = hosts in the ansible.cfg file and save and exit
    15 vi hosts -> delete everything by hitting shift :1,$d -> now add the private ip of webserver -> save and exit

    
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







