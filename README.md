# learn-ansible

Check if python is installed

````
python3.11 --version
````

Install python3.11 for Ansible 

````
sudo dnf install python3.11
````

Check pip versions available

````
pip3.11 --version
pip --version
````

Install pip3.11

````
sudo dnf install pip3.11
````

Install Ansible

```
sudo pip3.11 install ansible
```

To check version of Ansible that is installed

````
ansible --version
````

Create an inventory file 

````
vim servers
````

Check connectivity with the Node with Ping module (module denoted by -m). Ping should return pong. In case of small number of nodes, we can either go with an inventory file or specify the node IPs individually. When we list the individual IPs, each IP needs to be followed by a comma. 

````
ansible -i <inventory filename> all -e ansible_user=<username> -e ansible_password=<password> -m ping

ansible -i <node IP>, -e ansible_user=<username> -e ansible_password=<password> -m ping
````

To run a playbook 

````
ansible-playbook -i <inventory filename> -e ansible_user=<username> -e ansible_password=<password> frontend.yml

ansible-playbook -i <node IP>, -e ansible_user=<username> -e ansible_password=<password> frontend.yml
````
If certain commands in the playbook requires root privilege, use ansible privilege escalation: become to provide privileges.
https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_privilege_escalation.html

To run a playbook with a variable defined in main.yml

````
ansible-playbook -i <inventory filename> -e ansible_user=<username> -e ansible_password=<password> -e <var_name>=<var_value> main.yml

ansible-playbook -i <node IP>, -e ansible_user=<username> -e ansible_password=<password> -e <var_name>=<var_value> main.yml

#git pull; ansible-playbook -i frontend.dev.sridevops.site, -e ansible_user=ec2-user -e ansible_password=DevOps321 -e role_name=frontend main.yml#
````

Ansible vault - to encrypt files and strings. Limited to usage in ansible. 

Commands used:
````
ansible-vault --help 
ansible-vault create
ansible-vault encrypt - to encrypt a file
ansible-vault encrypt_string - to encrypt a string only
ansible-vault decrypt
ansible-vault view

````
Ansible vault - encrypt a file and call it in playbook

````
ansible-vault encrypt demo.yml
ansible-playbook demo.yml --ask-vault-password 
````

Ansible vault - encrypt a string

````
ansible-vault encrypt_string "Hello world"
````
Take the encrypted string and replace it in the file. Refer vault.yml for an example.

Ansible pull - ansible pulls from VCS and executes on target host

````
ansible-pull -i <host info>, -U <VCS URL> <path of playbook to be executed>

ansible-pull -i localhost, -U https://github.com/sridevopsb80/roboshop_ansible.git main.yml -e env=dev -e role_name=frontend
````

Manage parallelism - ansible connects to 5 remote hosts by default. To specify value:

````
ansible -f FORKS
````
Ansible conditionals - Refer when conditionals in roboshop-ansible ->common -> tasks -> golang.yml, java.yml, python.yml, schema.yml. Used in place of if, if-else and elif conditionals from the shell script.  
````
https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_conditionals.html
````


