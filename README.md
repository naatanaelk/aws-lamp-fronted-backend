# AWS-LAMP: Playbooks to provision a basic LAMP stack on AWS

There are two playbooks you need to run:
- provision-ec2.yml
- install-stack.yml

And one optional playbook you can run:
- configure-web.yml (On Progress)

## Installing Ansible on you Control Node
To use ansible automation script, you will need ansible on a separate machine or virtual machine. For this project, I am using ubuntu 20.04 as my Control Node. You can also use other linux machine for your control node. This playbook is working on:
- Ansible version >= 2.9
- Python version = 3.8.5

To install Ansible in ubuntu 20.04 is pretty simple:

```
sudo apt-get update
sudo apt-get install ansible
```

Check the installation by issuing:

```
ansible --version
```

## Provision EC2
This playbook creates one web server instances as a fronted, one MySQL instance as a db using the Ubuntu 18.04. It will also, if you want, provision a simple LAMP website to demonstrate the success of the installation.

You will need to install the boto Python & PyMySQL module first:

```
sudo apt-get install python3-pip
sudo pip3 install boto boto3
sudo apt-get install python3-pymysql
sudo apt-get update
```

You will also need to create user in AWS IAM (Identity and Access Management):
1. Go to IAM console in AWS console.
2. Access Management > Users > Add User.
3. Fill in username and pick programmatic access in access type.
4. Save the .csv credential.

Provisioning ec2 instance requires you to first update the following variables in group_vars/all:

- instance_type
- region
- key_pair
- security_group
- ec2_access_key (AWS Access Key)
- ec2_secret_key (AWS Secret Key)
- volume_size (optional)

To provision all instances, run:

```
ansible-playbook provision-ec2.yml
```

## Updating the Hosts
As I'm still figuring out how to use the ec2 dynamic inventory, updating hosts file needs to be done manually. After the provisioning have completed, add each server public ipv4 address to ANSIBLE_HOST parameter on the 'hosts' file.

Updating the hosts requires you to update the following variable in group_vars/all:

- db1_hosts
- web1_hosts

## Install the Stack
This playbook will install and configure all the necessary packages on the Apache, MySQL, and PHP.

First you need to disable ssh key checking by running:

```
export ANSIBLE_HOST_KEY_CHECKING=False
```

Then run the playbook:

```
ansible-playbook -i hosts install-stack.yml
```

Once the playbook has completed successfully, you can reach the site by going to

```
http://<public_ip_of_web_server>
```

## Configure the Website (Optional)
There are simple and ready to deploy website in this project. Keep in mind that this configuration only works for Ubuntu OS. For another linux os, you will need to reconfigure MySQL bind-address configuration yourself. To deploy, run:

```
ansible-playbook -i hosts configure-web.yml
```

After the playbook finished, check you website at:

```
http://<public_ip_of_web_server>
```
