# Ansible




![Ansible](https://user-images.githubusercontent.com/86292184/129172547-ccb278dc-626a-4f61-972a-5b9c9fa02dbb.png)




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
nginx_playbook.yml
```

- To check if nginx is active/isntalled, run
  - `ansible NAME_OF_INSTANCE -m shell -a "systemctl status nginx"`
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
## Install necessary packages
- cd /etc/ansible
- `sudo apt install python3-pip`
- `sudo pip3 install awscli `
- `sudo pip3 install boto boto3
`
- `sudo apt-get update -y `
- `sudo apt-get upgrade -y `
- To check version
- `aws --version`
<br> </br>
- ------------------------------------------------
### Create an ansible vault for aws keys
- Create new directory from `cd /etc/ansible`
  - `sudo mkdir group_vars`
  - Go into new directory
  - `cd group_vars/`
- Create new directory
  - `sudo mkdir all`
  - Go into new directory
  - `cd all/`
<br> </br>
- Now you should be in `cd /etc/ansible/group_vars/all`
- Create ansible vault
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
**TO RUN ANY COMMANDS FROM THIS POINT ON YOU NEED TO USE `--ask-vault-pass`**
- ------------------------------------------------
### Sync .pem file and create new SSH keys

- Sync .pem folder to controller
  - Navigate to where .pem file is on your local host and use `scp -i ~/.ssh/eng89_devops.pem -r eng89_devops.pem/ vagrant@CONTROLLER_IP:~/
`
  To move .pem file into .ssh in **controller**, run `mv name_of_key.pem .ssh/`

<br> </br>
- Create an ssh key in .ssh  in **controller**
  - Past `ssh-keygen -t RSA -C "nnikiforidi@spartaglobal.com"
` where you want to create SSH key pair ( in .ssh folder)
  - Enter file in which to save the key : eng89_devops
  - It'll prompt you with some more questions, press ENTER 2 times
  - Should should have your keys
<br> </br>
- -------------------------------------

### Create a playbook to launch an ec2 instance in Ireland region using your own SG, and subnet

```
ec2_playbook.yml
```
- The security group and subnet must allow ssh with your ip in inbound rules
<br> </br>
- To run playbook, run 
`sudo ansible-playbook ec2_playbook.yml --ask-vault-pass --tags create_ec2
`
- Since you have set up asible vault, you will need to use `--ask-vault-pass` 
<br> </br>
- ---------------------------------------------
### Update Hosts file
- In **controller**
- Add aws to host file
  - Go to cd /etc/ansible
  - Add this command to hosts file
  - `[aws]
ec2-instance ansible_host=INSTANCE_PUBLIC_IP ansible_user=ubuntu ansible_ssh_private_key_file=~/.ssh/eng89_devops.pem`
<br> </br>
- Ping instance to see if it works
- `sudo ansible all -m ping --ask-vault-pass
`
<br> </br>
- ----------------------------------------------
### SSH into app instance from controller 
<br> </br>
**MAKE SURE THE IPs IN YOUR SECURITY GROUP AND nACL ARE UP TO DATE**
- In **controller** ,navigate to .ssh file and SSH into instance `ssh -i "eng89_devops.pem" ubuntu@PUBLIC_INSTANCE_IP`
<br> </br>
- ------------------------------------------------
### Create playbook for DataBase instance
- In directory cd /etc/ansible, create new playbook
- `sudo ansible-playbook mongodb_playbook.yml --ask-vault-pass`

```
db_playbook.yml
```
- (When creating the playboof, I left the)
- Run teh playbook `sudo ansible-playbook db_playbook.yml --ask-vault-pass --tags create_ec2`

<br> </br>
- ------------------------------------------------
### Update Hosts file
- In **controller**
- Add aws to host file
  - Go to cd /etc/ansible
  - Add this command to hosts file
  - `[db]
ec2-instance ansible_host=INSTANCE_PUBLIC_IP ansible_user=ubuntu ansible_ssh_private_key_file=~/.ssh/eng89_devops.pem`
<br> </br>
- Ping instance to see if it works
- `sudo ansible all -m ping --ask-vault-pass
`
<br> </br>
**Make sure to change security group and nACL inabout/outboud rules so they can communicate**
- -----------------------------------------

### SSH into db instance from controller 

- In **controller** ,navigate to .ssh file and SSH into instance `ssh -i "eng89_devops.pem" ubuntu@PUBLIC_INSTANCE_IP`
<br> </br>
**MAKE SURE THE IPs IN YOUR SECURITY GROUP AND nACL ARE UP TO DATE**
- ------------------------------------------------------
### Setting up add and db to launch web app in browser

- In db instance:
  - Navigate to `cd /etc`
  - Open file `sudo nano mongod.conf`
  - Change bindIP to 0.0.0.0
  - `sudo systemctl restart mongod`
  - `sudo systemctl enable mongod`

<br> </br>

- In app instance
  - Create persistant variable:
    - run command `sudo echo export DB_HOST="mongodb://DB_INSTANCE_PUBLIC_IP:27017/posts" >> ~/.bashrc`
    - and `source ~/.bashrc`
  - Change defualt file
     - Navigate to `cd /etc/nginx/sites-available`
     - Chnge default file to 

     ```
server {
    listen 80;

    server_name _;
    location / {
        proxy_pass http://APP_INSTANCE_PUBLIC_IP:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}


     ```
     - Restart nginx `sudo systemctl restart nginx`
- Navigate to `cd /app`
- Run app with ` npm start`
<br></br>
- ---------------------------------------------------
**KEEP IN MIND**
- Personal IPs may change
- Update both security groups in aws: ssh -> YOUR_PRIVATE_IP and updadate db GS: 27017 -> PUBLIC_IP_APP_INSTANCE
- Update both nACLs: ssh -> YOUR_PRIVATE_IP
- Hosts file -> update both db and app instance ip 
- Persistant environment variable  -> update public ip for db instance
- Reverse proxi -> update pubib ip for app instance
