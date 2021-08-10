






![Ansible](https://user-images.githubusercontent.com/86292184/128696124-b0d2cfdf-6096-48b9-8865-47316f301d35.png)


<br> </br>

### Setting up Ansible in Controller

- Run the below commands
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
- 

- Navigate to cd/etc/ansible 

















 - -----------------------
 ansible all -a "uname -a"



- ----------------
###Task pseudo code

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

- ----------------


<br> </br>
# Ansible controller and agent nodes set up guide
- Clone this repo and run `vagrant up`
- `(double check syntax/intendation)`

## We will use 18.04 ubuntu for ansible controller and agent nodes set up 
### Please ensure to refer back to your vagrant documentation

- **You may need to reinstall plugins or dependencies required depending on the OS you are using.**

```vagrant 
# -*- mode: ruby -*-
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
