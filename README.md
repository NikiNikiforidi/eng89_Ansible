# Ansible




![Ansible](https://user-images.githubusercontent.com/86292184/128696124-b0d2cfdf-6096-48b9-8865-47316f301d35.png)



### infrastructure as code
- Configurations management:
  - Ansible (Does config.  management for hundrends of servers)

- Orchestration management:
- Terraform


<br> </br>
- ----------------------------------------------------------


### Vagrant file to set up 3 VMs

```


# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what

# MULTI SERVER/VMs environment 
#
Vagrant.configure("2") do |config|

# creating first VM called web  
  config.vm.define "web" do |web|
    
    web.vm.box = "bento/ubuntu-18.04"
   # downloading ubuntu 18.04 image

    web.vm.hostname = 'web'
    # assigning host name to the VM
    
    web.vm.network :private_network, ip: "192.168.33.10"
    #   assigning private IP
    
    web.vm.synced_folder "/Users/Niki/eng89_Ansible/app", "/home/vagrant/app"


    config.hostsupdater.aliases = ["development.web"]
    # creating a link called development.web so we can access web page with this link instread of an IP   
        
  end
  
# creating second VM called db
  config.vm.define "db" do |db|
    
    db.vm.box = "bento/ubuntu-18.04"
    
    db.vm.hostname = 'db'
    
    db.vm.network :private_network, ip: "192.168.33.11"
    
    config.hostsupdater.aliases = ["development.db"]     
  end

 # creating are Ansible controller
  config.vm.define "controller" do |controller|
    
    controller.vm.box = "bento/ubuntu-18.04"
    
    controller.vm.hostname = 'controller'
    
    controller.vm.network :private_network, ip: "192.168.33.12"
    
    config.hostsupdater.aliases = ["development.controller"] 
    
  end

end
```
- SSH into each VM 
- Run `sudo apt-get update` in all three VMs to check if they can connect to the internet
<br> </br>
- --------------------------------------------
### Setting up Ansible 
- Run the below commands in **Controller** VM
- This only would work for vagrant
```
sudo apt-get update
  
sudo apt-get install software-properties-common
  
sudo apt-add-repository ppa:ansible/ansible
  
sudo apt-get update
  
sudo apt-get install ansible

```
- To check if it's installed, run:
` ansible--version`
<br> </br>
- ------------------------------------------------------
### Navigate to hosts file

- Navigate to `cd /etc/ansible`
- To check the files whithin the folder run `ls`
- There is a more convenient way, although unnecessary, to see the files (SeeInstall Tree )
<br> </br>
- -------------------------------------
### Install Tree

- A more visual way to see files in foldesr
- Install `sudo apt-get install tree`
- Once you have installed it, run `tree`
- You'll get a clear overview of your file structure
<br> </br>
- ------------------------------
### To ping IP addresses 
- Run `ping IP_ADDRESS`
- To exit ping, ctrl +c
<br> </br>

- --------------------------------------------
### Hosts file
- In **controller** VM
- Navigate to cd /etc/ansible
- Trying to run `ansible all ping -m ping` will return an error
- Hosts list is empty
- We have to add the server IPs so the ansible controller can communicate with them
<br> </br>
**SSh into servers from ansible controller**
- While in **controller** VM -> /etc/ansible:
- SSH into web server
  - `ssh vagrant@192.168.33.10` IP for web
  - You will be required to enter a password
- exit
- SSH into db server
  - `ssh vagrant@192.168.33.11` IP for db
  - You will be required to enter a password
- exit
<br> </br>
**Add IPs into host file**
- Run `sudo nano hosts`
- ADD 
```

[web]
192.168.33.10 ansible_connection=ssh ansible_ssh_user=vagrant ansible_ssh_pass= ENTER_PASSWORD


[db]
192.168.33.11 ansible_connection=ssh ansible_ssh_user=vagrant ansible_ssh_pass=ENTER_PASSWORD

```
- Now that you had added the servers, you can try ping them
- To ping web, run `ansible web -m ping`
- You should get a successful  `pong` response.
- 
<br> </br>

- -------------------------------------------
### Some Ad Hoc commands
- To get a list of all your machine names and more info:
  - In **controller** -> /etc/ansible, run:
  - `ansible all -a "uname -a"`
  <br> </br>
- To check time/ date of web machine, run
  -  `ansible web -a "date" ` 
<br> </br>
- To check more information, run
   - `ansible all -m shell -s "free -m"`
<br> </br>
- To check all the files whithin the machines,run
  - `ansible all -m shell -s "ls -a"`
<br> </br>
- ---------------------------------------------------------
### Setting up ansible Playbook for nginx
- Go to: `cd /etc/ansible`
- Create a file `sudo nano nginx_playbook.yml`
- **Indentation is very important**
```
CHECK OUT NGINX_PLAYBOOK.YML FILE
```

- To check if nginx is active/isntalled, run
  - `ansible web -m shell -a "systemctl status nginx"`
<br> </br>

- To run playbook, run 
`ansible-playbook nginx_playbook.yml`
<br> </br>

- Put web IP in browser to check if nginx is working, `192.168.33.10`
<br> </br>
- ----------------
### Task pseudo code

On the web server  we would like to install node js with required dependencies so we could launch the nodeapp on web server's IP
- The moving onto configuration reverse proxy eiyh nginx so we would launch the app on port 80 instead of 3000
- hosts : web
- gather_facts: yes

- Instructions in this playbook to install modejs
- tasks:install nodejs


- shell:
- copy the app code to web server( you could close it from repo, copy from your controller to web or os to web)
- go toright location (cd) to install npm, ac app
- npm start
<br> </br>

- ---------------------------------------------
## Create an ansible vault for aws keys
- cd /etc/ansible
- `sudo apt install python3-pip`
- `sudo pip3 install awscli `
- `sudo pip3 install boto boto3
`
- `sudo apt-get update -y `
- `sudo apt-get upgrade -y `
- To check version
- `aws --version`
- Create new directory
  - `sudo mkdir group_vars`
  - Go into new directory
  - `cd group_vars/`
- Create new directory
  - `sudo mkdir all`
  - Go into new directory
  - `cd all/`
- Now you should be in `cd /etc/ansible/group_vars/all`
<br> </br>
- **To create ansible vault**
- `sudo ansible-vault create pass.yml`
  - It will ask to enter new Vault password: ENTER_PASSWORD
  - It will ask to confirm passwork: REENTER PASSWROD

  - A text edit will open, press i (-- INSERT--) and add :
  ```
  aws_access_key: 
  aws_secret_key:
  ```
- **MAKE SURE TO NEVER SHARE YOUR AWS ACCESS AND SECRET KEYS**
  - To save and exit editor
    - press `esc`
    - type `:wq!`
    - press enter
    <br> </br>
- If you try `cat pass.yml` you should be blocked to seeing the keys
<br> </br>
- ------------------------------------------------
### Create a playbook to launch an ec2 instance in Ireland region using your own SG, and subnet

# playbook for launchin an aws ec2 instance

```
---

- hosts: localhost
  connection: local
  gather_facts: yes
  become: true
  vars:
    key_name: eng89_devops
    region: eu-west-1
    image: ami-039900c4ef89c6f9c
    id: "eng89 ansible playbook to launch an aws ec2 instance"
    sec_group: "sg-0e4a4d95cfa2c3ec0"
    subnet_id: "subnet-00ac052b1e40c0164"
    ansible_python_interpreter: /usr/bin/python3
  tasks:

    - name: Facts
      block:

      - name: Get instance facts
        ec2_instance_facts:
          aws_access_key: "{{aws_access_key}}"
          aws_secret_key: "{{aws_secret_key}}"
          region: "{{ region }}"
        register: result

    - name: provisioning ec2 instances
      block:

      - name: upload public key to aws_access_key
        ec2_key:
          name: "{{ key_name }}"
          key_material: "{{ lookup('file', ~./ssh/{{ key_name }}.pub') }}"
          region: "{{ region }}"
          aws_access_key: "{{aws_access_key}}"
          aws_secret_key: "{{aws_secret_key}}"

      - name: provision instance
        ec2:
          aws_access_key: "{{aws_access_key}}"
          aws_secret_key: "{{aws_secret_key}}"
          assign_public_ip: True
          key_name: "{{ key_name }}"
          id: "{{ id }}"
          vpc_subnet_id: "{{ subnet_id }}"
          group_id: "{{ sec_group }}"
          image: "{{ image }}"
          instance_type: t2.micro
          region: "{{ region }}"
          wait: True
          count: 1
          instance_tags:
            Name: eng89_niki_ansible_playbook
            
      tags: ['never', 'create_ec2']
```