# Introduction

This Ansible project allow you to install a Wiseflat Server for your personal projects.
Because we love automation and monitoring, we provide all our best tools to make your life easy.

With our projet, you will be able to :

- install and configure a raspbian server with LXD
- download LXC microservices from our API or create your home made lxc containers.

# Ansible

All our API can be used with a simple curl command but you will need quickly a strong and scalable tool to stop doing multiple times the same things. For that, we use Ansible. This is a software that automates software provisioning, configuration management, and application deployment. 

# Let's start !

### Boot up your raspberrypi

This is a requirement. You need to configure Wiseflat Server on a ARM architecture.
Be sure the raspberrypi got an IP address on your lan and ssh server enabled and started.

# Conventions

This project contains a default ssh/config file. 
**All our servers and containers have a prefix named "home-". If you change this you will have to update your ansible inventory.**
Pi hostname is **home-wiseflat**

### Install ansible

Latest Releases Via Apt (Ubuntu)
```
$ sudo apt-get update
$ sudo apt-get install software-properties-common
$ sudo apt-add-repository ppa:ansible/ansible
$ sudo apt-get update
$ sudo apt-get install ansible
```

Latest Releases on Centos
```
$ sudo yum install epel-release
$ sudo yum install ansible
```

Latest Releases on Mac OSX
```
$ brew install ansible
```

### Clone this git project

```
$ cd ~
$ git clone https://github.com/wiseflat/wiseflat-project.git
$ cd wiseflat-project
```

Create your own git branch and work on it

```
$ git branch me 
$ git checkout me
```

### SSH configuration

Ansible is based on SSH. You need to autorise ansible to connect to your server, so create ssh keys for this ansible project
```
$ ssh-keygen -f ssh/id_rsa -P ""
```
Then, autorise your local user to connect to this server. It will add your public key to ~/.ssh/authorized_keys
```
$ ssh-copy-id -i ssh/id_rsa.pub pi@home-wiseflat
```

Now you should be able to connect to your server
```
$ ssh -F ssh/config home-wiseflat
```

### Let's play

```
$ ansible-playbook site.yml
```