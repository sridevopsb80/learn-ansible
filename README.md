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

Check connectivity with the Node with Ping module. Ping should return pong.

````
ansible -i <inventory filename> all -e ansible_user=<username> -e ansible_password=<password> -m ping

ansible -i <node IP>, -e ansible_user=<username> -e ansible_password=<password> -m ping
````

To run a playbook 

````
ansible-playbook -i <inventory filename> -e ansible_user=<username> -e ansible_password=<password> frontend.yml

ansible-playbook -i <node IP>, -e ansible_user=<username> -e ansible_password=<password> frontend.yml
````
To run a playbook with a variable defined in main.yml

````
ansible-playbook -i <inventory filename> -e ansible_user=<username> -e ansible_password=<password> -e <var_name>=<var_value> main.yml

ansible-playbook -i <node IP>, -e ansible_user=<username> -e ansible_password=<password> -e <var_name>=<var_value> main.yml


````

