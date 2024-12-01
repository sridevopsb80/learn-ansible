# learn-ansible

## Python and ansible installation 

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

## Create an inventory file, check connectivity, privilege escalation and how to run a playbook

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
Documentation: https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_privilege_escalation.html

To run a playbook with a variable defined in main.yml

````
ansible-playbook -i <inventory filename> -e ansible_user=<username> -e ansible_password=<password> -e <var_name>=<var_value> main.yml

ansible-playbook -i <node IP>, -e ansible_user=<username> -e ansible_password=<password> -e <var_name>=<var_value> main.yml

#git pull; ansible-playbook -i frontend.dev.sridevops.site, -e ansible_user=ec2-user -e ansible_password=DevOps321 -e role_name=frontend main.yml#
````
## Ansible vault

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

## Ansible pull

Ansible pull - ansible pulls from VCS and executes on target host

````
ansible-pull -i <host info>, -U <VCS URL> <path of playbook to be executed>

ansible-pull -i localhost, -U https://github.com/sridevopsb80/roboshop_ansible.git main.yml -e env=dev -e role_name=frontend
````
## Manage parallelism

Manage parallelism - ansible connects to 5 remote hosts by default. To specify value:

````
ansible -f FORKS
````
## Ansible conditionals

Ansible conditionals - Refer when conditionals in roboshop-ansible ->common -> tasks -> golang.yml, java.yml, python.yml, schema.yml. Used in place of if, if-else and elif conditionals from the shell script.
Documentation: https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_conditionals.html
````
- name: Load Schema
  ansible.builtin.include_role: 
    name: common
    tasks_from: schema
  when: schema_setup is defined 
  #conditional used to load schema only when schema_setup is defined in a component
````
## Grouping tasks in Ansible using blocks

Documentation: https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_blocks.html#grouping-tasks-with-blocks
https://linuxhandbook.com/ansible-blocks/

Block is used to group tasks. The below block is preceded by a conditional. The block is executed when the condition is met. Here, the variable value of schema_setup needs to be mysql.
````
- name: Load MySQL schema
  when: schema_setup == "mysql"
  block:
    - name: Install mysql Client
      ansible.builtin.dnf:
        name: mysql
        state: latest

    - name: Load Master Data
      ansible.builtin.shell: mysql -h mysql.{{ env }}.sridevops.site -uroot -pRoboShop@1 < /app/db/{{ item }}.sql
      loop: "{{schema_files}}"
````
## Ansible loops

Similar to using for loop in shell, in the above example, we are using loop in ansible. The value for loop will be obtained from the variable schema_files. 'Item' variable comes with loop and is used instead of file var in shell.

## Ansible include role vs Role dependencies 

Ansible include role is being used to load a task from another role. In the below example, we are loading app-prereq.yml from common->tasks->app-prereq.yml. When running a role, we are including another role.
````
ansible.builtin.include_role:
  name: common
  tasks_from: app-prereq
````
Ansible role dependency is used to load main.yml from common->tasks->main.yml. Role dependencies are used to load other roles as prerequisites while running a role. The prerequisite role will run before the role that is calling it. Role dependency is defined in the meta folder of the role that is calling the prerequisite role. It always calls the main.yml file in the role that is being called.
In the below example (roboshop_ansible->redis->meta->main.yml), redis is using common as a role dependency to run the set-prompt command prior to execution of redis role. 

roboshop_ansible->redis->meta->main.yml
````
dependencies:
  - role: common
````
roboshop_ansible->common->main.yml
````
- name: Set Prompt
  ansible.builtin.shell: set-prompt {{component}}-{{env}}
````
## Hashicorp vault

````
    msg: "{{ lookup('hashi_vault', 'secret=kv/data/test:MYPASS token={{vault_token}} url=https://vault-internal.sridevops.site:8200 validate_certs=false')}}"
````
In order to use hashi_vault lookup module, install hvac.

````
sudo pip3.11 install hvac
````

