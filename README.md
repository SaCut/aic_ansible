# Ansible

#### What is Ansible?
- Ansible is an open-source software provisioning, configuration management, and application-deployment tool enabling infrastructure as code. It runs on many Unix-like systems, and can configure both Unix-like systems as well as Microsoft Windows. It includes its own declarative language (YAML, Yet Another Markup Language) to describe system configuration.

#### What are the benefits of Ansible?
- simple (uses yaml, human readable)
- agentless (you do not need to interact with your agent nodes)
- automation tool

#### Ansible diagram
- ![img](https://imgur.com/ff5S2I6.png)

#### after vagrant up
- `ssh vagrant@CONTROLLER_IP`
```shell
sudo apt-get update

sudo apt-get upgrade -y

sudo apt-get install software-properties-common -y

sudo apt-add-repository ppa:ansible/ansible -y

sudo apt-get update

sudo apt-get install ansible -y

sudo apt-get install python3 python3-pip -y

sudo pip3 install boto3
```

#### notes
- enter into the ansible controller `vagrant ssh controller`
- enter into a machine `ansible vagrant@MACHINE_IP`
- ad-hoc command is a small occasional command that doesn't need a provision/playbook
- playbooks are basically provisions, a list of multiple commands
- hosts file is called inventory file, any guest we would like to connect to is inserted here
- to check connection (from controller): `ansible MACHINE_NAME -m ping`
- to run ad-hoc command from ansible controller into machine: `ansible MACHINE_NAME -a "COMMAND"`
- example: `ansible web -a "free -m"` gives the available RAM memory
- another example: `ansible web -m shell -a "ls -a"`
- check yaml file syntax: `ansible-playbook FILE_NAME.yml --syntax-check`
- run playbook with tags: `ansible-playbook FILE_NAME.yml --tags "TAG_1, TAG_2"`
- excluding tags: `ansible-playbook FILE_NAME.yml --skip-tags "TAG_1, TAG_2"`

#### task
- find out uptime: `ansible all -a "uptime"`
- update and upgrade: `ansible all -m shell -a "sudo apt-get update -y && sudo apt-get upgrade -y"`

#### Playbooks
- playbooks are stored in `/etc/ansible/`
- `/etc/ansible/` tree:
```shell
.
├── ansible.cfg
├── group_vars
│   └── all
│       └── pass.yml
├── hosts
├── install_mongodb.yml
├── install_nginx.yml
├── install_nodejs.yml
├── install_sql.yml
├── roles
└── setup_node_and_db.yml
```
- inside `hosts`:
```
[web]
192.168.33.10 ansible_connection=ssh ansible_ssh_user=vagrant ansible_ssh_pass=vagrant

[db]
192.168.33.11 ansible_connection=ssh ansible_ssh_user=vagrant ansible_ssh_pass=vagrant
```
- inside `install_mongodb.yml`:
```yml
---
# Installing Mongodb for our db to connect to the web app for "/posts"

- hosts: db
  gather_facts: true
  become: true
  
  tasks:
  - name: Installing Mongodb into DB server 192.168.33.11
    apt: pkg=mongodb state=present
```
- inside `install_nginx.yml`:
```yml
# This is an example of an ansible playbook written in YAML
# YAML files start with three dashes

---

# hosts defines the name of your host machine
- hosts: web

  # gathering facts before performing any task
  gather_facts: yes

  # be administrator
  become: true

  # 
  tasks:
    # tasks are executed in order, one at a time, against all ser$
    # every task shoud have a name, which is included in the outp$
    # the goal of each task is to execute a module with specific $

    # in this task we would like to install nginx on our web serv$
    - name: Installing NginX
      apt: pkg=nginx state=present
```
- inside `install_nodejs.yml`:
```yml
---
  - hosts: web
    gather_facts: true
    become: true
    
    tasks:
    - name: Installing nodejs in "web" server
      apt: pkg=nodejs state=present
      
    - name: Installing NPM
      apt: pkg=npm state=present
```
- inside `install_sql.yml`:
```yml
---
# install SQL on db machine
- hosts: db
# hosts will look for db in inventory file

  # run as admin
  become: true
  
  # instructions/code/script to install SQL
  tasks:
  - name: installing SQL in the DB server
    
    # the db will have the sql installed and enabled
    apt: pkg=mysql-server state=present
```
- to run a `.yml` file with ansible: `sudo ansible-playbook FILE_NAME.yml`

#### vault (for AWS)
- in `/etc/ansible/` create a directory called `group_vars`, and another called `all` inside it
- create a file called `pass.yml` with this command: `sudo ansible-vault create pass.yml`
- to edit this file: `sudo ansible-vault edit group_vars/all/pass.yml`
- here press the "i" key to enter insert mode, then add the lines:
```
aws_access_key: MY_ACCESS_KEY
aws_secret_key: MY_SECRET_KEY
```
the save by pressing "esc" and inserting :wq
- After this, many sensitive ansible commands will require the additional string `--ask-vault-pass` + the password

#### AWS task
- Launch an ec2 instance (controller) either from your local machine (using or another ec2 instance on AWS
- use ansible playbooks to launch another 2 machines on AWS (app and database)
- implement 2 tier architecture as IAC with Ansible
- nodeapp to work with public ip and db to work with /posts

- deadline to submit the task is 9am 06/05/2021

#### solution
- `ssh -i ~/.ssh/eng84devops ubuntu@PUBLIC_IP`
- - update instances IPs in `/etc/ansible/hosts`

- if new controller:
    - create new file with `sudo ansible-vault create /etc/ansible/group_vars/pass.yml` + passwd + credentials
    - run `scp -ri eng84devops.pem /home/saverio/Projects/sparta-global/aic-ansible/ ubuntu@PUBLIC_ID_OF_CONTROLLER:/etc/ansible/`
    - create instances `ansible-playbook PLAYBOOK_NAME.yml --ask-vault-pass --tags "TAG_1, TAG_2"/TAG`
- run `sudo ansible-playbook /etc/ansible/db_provision.yml`, then `sudo ansible-playbook /etc/ansible/app_provision.yml` 

#### Amazon Cloudwatch
![img](https://i.imgur.com/0VCHhQU.png)
