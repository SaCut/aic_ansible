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

- 
```shell
sudo apt-get update

sudo apt-get upgrade -y

sudo apt-get install software-properties-common

sudo apt-add-repository ppa:ansible/ansible -y

sudo apt-get update

sudo apt-get install ansible -y
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

#### task
- find out uptime: `ansible all -a "uptime"`
- update and upgrade: `ansible all -m shell -a "sudo apt-get update -y && sudo apt-get upgrade -y"`
