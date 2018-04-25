![Basic LAN](https://wiki.wiseflat.com/img/logo.png "Logo")

<h1>A toolbox for your IT</h1> 

Wiseflat is a combination of software programs to help you host your home automated systems and to protect you and your family from the internet

## About

Wiseflat server is an entry point for your automated systems and your web applications. It allows you to deploy LXC containers in order to add features you need for your home.

```
pi@home-wiseflat:~ $ lxc list
+--------------+---------+------------------------+------+------------+-----------+
|     NAME     |  STATE  |          IPV4          | IPV6 |    TYPE    | SNAPSHOTS |
+--------------+---------+------------------------+------+------------+-----------+
| home-blog    | RUNNING | 192.168.1.210 (eth0)   |      | PERSISTENT | 0         |
+--------------+---------+------------------------+------+------------+-----------+
| home-cms     | RUNNING | 192.168.1.192 (eth0)   |      | PERSISTENT | 0         |
+--------------+---------+------------------------+------+------------+-----------+
| home-dnsmasq | RUNNING | 192.168.1.240 (eth0)   |      | PERSISTENT | 0         |
+--------------+---------+------------------------+------+------------+-----------+
| home-jeedom  | RUNNING | 192.168.1.138 (eth0)   |      | PERSISTENT | 0         |
+--------------+---------+------------------------+------+------------+-----------+
| home-wiki    | RUNNING | 192.168.1.197 (eth0)   |      | PERSISTENT | 0         |
+--------------+---------+------------------------+------+------------+-----------+
```


To install a Wiseflat server, an Ansible project is available to limit technical issues and gain time. It's a project that allows everyone, experts or beginners to build their own Linux server with containerized applications.

This Ansible project allows you to:

- preconfigure your server with the basic software
- configure LXD to start LXC containers
- install microservices to meet your needs

Here is a non-exhaustive list of microservices available to you:

- Totaljs software suite (cms, wiki, blog, messenger)
- home automation applications (jeedom, domoticz, homebridge, nodered)
- communication gateways (mqtt, rabbitmq, xPL)
- services to protect your privacy and security tools (dnsmasq, iptables rules)


## Basics

### Local area network

A local network is generally composed of a wifi router allowing computers, tablets, smartphones to access the internet.

The wifi router is normally provided by your ISP, it only contains few but essential options. Its role is limited to protecting you from the internet thanks to a firewall. It also has a DHCP role to provide IP addresses to the machines on your LAN and also the DNS role to ensure the domain name resolutions.

![Basic LAN](https://wiki.wiseflat.com/img/lan1-1.png "Basic LAN")

### Linux containers

Since the apparition of Linux 2.6.24 kernel, LXC has provided support for containerization to provide OS-level virtualization and allows a single host to run multiple isolated Linux instances, known as Linux containers or LXC (LinuX Containers).

In our case, the container is a very interesting approach since it allows us to multiply the number of linux servers on a single raspberry pi.

If the CPU/RAM resources of a single raspberrypi are not enough for your needs, just add a new one.

![Basic LAN](https://wiki.wiseflat.com/img/lan2-1.png "Basic LAN")

### Ansible management

Setting a server up manually can be difficult, especially if you have several machines to configure in the same way. Building a production-ready application can be hard if you are not an expert. If you need to do that more than once, it's a good training but it gets boring...

A lot of people have looked into this issue and built solutions to automate deployment (puppet, chef, salt). My personal favorite is **Ansible**.

Just write your specifications in your favorite code editor and let the magic happens ! 

Ansible will help you take a step back and think about what you need to do next. It will Help you focus on the essential. 

Once tasks are written in a Ansible playbook, your will be able to execute the same tasks to a thousand of machines if you need ! Ansible will connect to each of your servers using ssh to perform the job.

Seriously, Ansible changed my life...

![Basic LAN](https://wiki.wiseflat.com/img/lan3-1.png "Basic LAN")

## Install

### Requirements

You need to configure your Wiseflat Server on an ARM architecture like a raspberry pi.
This is a requirement because our LXC microservices are built on arm architecture.

Be sure the raspberrypi got an IP address on your lan from your dhcp server, the ssh server should be enabled and started.

### Conventions

This git project contains a default ssh/config file. 

**All our servers and containers have a prefix named "home-".**

If you change this, you will have to update your Ansible inventory env/inventory.ini

Pi hostname is **home-wiseflat**

### Installing Ansible

Latest Releases Via Apt (Ubuntu)

```sh
$ sudo apt-get update
$ sudo apt-get install software-properties-common
$ sudo apt-add-repository ppa:Ansible/Ansible
$ sudo apt-get update
$ sudo apt-get install Ansible
```

Latest Releases on Centos

```sh
$ sudo yum install epel-release
$ sudo yum install Ansible
```

Latest Releases on Mac OSX

```sh
$ brew install Ansible
```

### Cloning wiseflat-project

```sh
$ cd ~
$ git clone https://github.com/wiseflat/wiseflat-project.git
$ cd wiseflat-project
```

### SSH configuration

Ansible is based on SSH. You need to allow Ansible to connect to your server without a password, so create ssh keys for this Ansible project

```sh
$ ssh-keygen -f ssh/id_rsa -P ""
```
Then allow your local user to connect to this server. It will add your public key to ~/.ssh/authorized_keys

```sh
$ ssh-copy-id -i ssh/id_rsa.pub pi@home-wiseflat
```

Now you should be able to connect to your server

```sh
$ ssh -F ssh/config home-wiseflat
```

### Let's play

```sh
$ Ansible-playbook site.yml

PLAY [server] ********************************************************************************************************

TASK [Gathering Facts] ***********************************************************************************************
ok: [home-wiseflat]

TASK [Set timezone] **************************************************************************************************
ok: [home-wiseflat]

TASK [Update sources.list] *******************************************************************************************
ok: [home-wiseflat]

TASK [Remove unused packages] ****************************************************************************************

TASK [Install useful packages] ***************************************************************************************
ok: [home-wiseflat] => (item=[u'htop', u'vim', u'tmux', u'unattended-upgrades', u'aptitude', u'git', u'snapd', u'ntp', u'jq'])

TASK [Configure network interface] ***********************************************************************************
ok: [home-wiseflat]

TASK [Install lxd via snap] ******************************************************************************************
changed: [home-wiseflat]

TASK [Wait few seconds for snapd] ************************************************************************************
ok: [home-wiseflat -> localhost]

TASK [Append the group 'lxd' to the user pi] *************************************************************************
ok: [home-wiseflat]

TASK [include_tasks] *************************************************************************************************
included: /home/me/wiseflat-project/play/tasks/noreboot.yml for home-wiseflat

TASK [Check if a reboot is required] *********************************************************************************
ok: [home-wiseflat]

TASK [Create noreboot file] ******************************************************************************************
skipping: [home-wiseflat]

TASK [Reboot] ********************************************************************************************************
skipping: [home-wiseflat]

TASK [sleep for 60 seconds] ******************************************************************************************
skipping: [home-wiseflat]

TASK [Init LXD daemon] ***********************************************************************************************
changed: [home-wiseflat]

TASK [Add local daemon as a remote server] ***************************************************************************
changed: [home-wiseflat]

TASK [Update default lxd profile] ************************************************************************************
changed: [home-wiseflat]

PLAY [User variables] ************************************************************************************************

TASK [Gathering Facts] ***********************************************************************************************
ok: [localhost]

TASK [pwd] ***********************************************************************************************************
changed: [localhost]

TASK [set_fact] ******************************************************************************************************
ok: [localhost]

TASK [file] **********************************************************************************************************
changed: [localhost]

TASK [pause] *********************************************************************************************************
skipping: [localhost]

TASK [USER | update email to inventory] ******************************************************************************
skipping: [localhost]

TASK [pause] *********************************************************************************************************
skipping: [localhost]

TASK [USER | update firstname to inventory] **************************************************************************
skipping: [localhost]

TASK [pause] *********************************************************************************************************
skipping: [localhost]

TASK [USER | update lastname to inventory] ***************************************************************************
skipping: [localhost]

TASK [pause] *********************************************************************************************************
skipping: [localhost]

TASK [USER | update label to inventory] ******************************************************************************
skipping: [localhost]

TASK [pause] *********************************************************************************************************
skipping: [localhost]

TASK [USER | update latitude to inventory] ***************************************************************************
skipping: [localhost]

TASK [pause] *********************************************************************************************************
skipping: [localhost]

TASK [USER | update longitude to inventory] **************************************************************************
skipping: [localhost]

TASK [USER | update heartbeat to inventory] **************************************************************************
skipping: [localhost]

TASK [USER | update airquality_request to inventory] *****************************************************************
skipping: [localhost]

TASK [USER | update suncalc_request to inventory] ********************************************************************
skipping: [localhost]

TASK [USER | update weather_request to inventory] ********************************************************************
skipping: [localhost]

TASK [USER | update cron to inventory] *******************************************************************************
skipping: [localhost]

PLAY [server] ********************************************************************************************************

TASK [Gathering Facts] ***********************************************************************************************
ok: [home-wiseflat]

TASK [pwd] ***********************************************************************************************************
changed: [home-wiseflat -> localhost]

TASK [set_fact] ******************************************************************************************************
ok: [home-wiseflat]

TASK [USER | get personnal token] ************************************************************************************
skipping: [home-wiseflat]

TASK [debug] *********************************************************************************************************
skipping: [home-wiseflat]

TASK [USER | update token to inventory] ******************************************************************************
skipping: [home-wiseflat]

TASK [set_fact] ******************************************************************************************************
skipping: [home-wiseflat]

PLAY [server] ********************************************************************************************************

TASK [Gathering Facts] ***********************************************************************************************
ok: [home-wiseflat]

TASK [uri] ***********************************************************************************************************
ok: [home-wiseflat]

TASK [debug] *********************************************************************************************************
ok: [home-wiseflat] => {
    "msg": {
        "message": "done",
        "status": "success"
    }
}

TASK [Wait until you receive your email registration or continue if your account is already activated] ***************
[Wait until you receive your email registration or continue if your account is already activated]
Press enter to continue:

ok: [home-wiseflat]

PLAY [server] ********************************************************************************************************

TASK [Gathering Facts] ***********************************************************************************************
ok: [home-wiseflat]

TASK [uri] ***********************************************************************************************************
ok: [home-wiseflat]

TASK [debug] *********************************************************************************************************
ok: [home-wiseflat] => {
    "msg": {
        "message": "done",
        "status": "success"
    }
}

PLAY [server] ********************************************************************************************************

TASK [Gathering Facts] ***********************************************************************************************
ok: [home-wiseflat]

TASK [uri] ***********************************************************************************************************
ok: [home-wiseflat]

TASK [debug] *********************************************************************************************************
ok: [home-wiseflat] => {
    "msg": {
        "message": "done",
        "status": "success",
        "value": {
            "counter": {
                "lastday": "0",
                "lasthour": "0",
                "lastminute": "25",
                "lastmonth": "0"
            },
            "email": "john@wiseflat.com",
            "firstname": "John",
            "ip": "1.1.1.1",
            "label": "john-wiseflat-project-server1",
            "lastname": "Doe"
        }
    }
}

PLAY [server] ********************************************************************************************************

TASK [Gathering Facts] ***********************************************************************************************
ok: [home-wiseflat]

TASK [uri] ***********************************************************************************************************
ok: [home-wiseflat]

TASK [debug] *********************************************************************************************************
ok: [home-wiseflat] => {
    "msg": {
        "message": "done",
        "status": "success",
        "value": {
            "counter": {
                "lastday": "0",
                "lasthour": "0",
                "lastminute": "26",
                "lastmonth": "0"
            },
            "email": "john@wiseflat.com",
            "firstname": "John",
            "ip": "1.1.1.1",
            "label": "john-wiseflat-project-server1",
            "lastname": "Doe"
        }
    }
}

PLAY [localhost] *****************************************************************************************************

TASK [Gathering Facts] ***********************************************************************************************
ok: [localhost]

TASK [uri] ***********************************************************************************************************
ok: [localhost]

TASK [debug] *********************************************************************************************************
ok: [localhost] => {
    "msg": {
        "message": "done",
        "status": "success",
        "value": []
    }
}

PLAY [server] ********************************************************************************************************

TASK [Gathering Facts] ***********************************************************************************************
ok: [home-wiseflat]

TASK [uri] ***********************************************************************************************************
ok: [home-wiseflat]

TASK [debug] *********************************************************************************************************
ok: [home-wiseflat] => {
    "msg": {
        "message": "done",
        "status": "success",
        "value": {
            "message": "Your personnal heartbeat is disable",
            "status": 0,
            "timestamp": "Friday, April 20, 2018 12:03 AM"
        }
    }
}

PLAY [localhost] *****************************************************************************************************

TASK [Gathering Facts] ***********************************************************************************************
ok: [localhost]

TASK [uri] ***********************************************************************************************************
ok: [localhost]

TASK [debug] *********************************************************************************************************
ok: [localhost] => {
    "msg": {
        "message": "done",
        "status": "success",
        "value": [
            {
                "description": "Your personnal totaljs wiki microservice",
                "label": "wiki"
            },
            {
                "description": "Your personnal totaljs blog microservice",
                "label": "blog"
            },
            {
                "description": "Your personnal totaljs cms microservice",
                "label": "cms"
            },
            {
                "description": "Your personnal jeedom microservice",
                "label": "jeedom"
            },
            {
                "description": "Your personnal totaljs messenger microservice",
                "label": "messenger"
            },
            {
                "description": "Your personnal dnsmasq/etcd router",
                "label": "router"
            }
        ]
    }
}
microservice name please : cms

PLAY [server] ********************************************************************************************************

TASK [Gathering Facts] ***********************************************************************************************
ok: [home-wiseflat]

TASK [Clean up] ******************************************************************************************************
ok: [home-wiseflat]

TASK [Download microservice] *****************************************************************************************
changed: [home-wiseflat]

TASK [Delete old lxc image if exists] ********************************************************************************
fatal: [home-wiseflat]: FAILED! => {"changed": true, "cmd": ["/snap/bin/lxc", "image", "delete", "cms"], "delta": "0:00:00.363338", "end": "2018-04-20 12:54:18.091785", "msg": "non-zero return code", "rc": 1, "start": "2018-04-20 12:54:17.728447", "stderr": "Error: not found", "stderr_lines": ["Error: not found"], "stdout": "", "stdout_lines": []}
...ignoring

TASK [Import microservice to lxd image store] ************************************************************************
changed: [home-wiseflat]

TASK [Clean up] ******************************************************************************************************
changed: [home-wiseflat]

TASK [Create microservice] *******************************************************************************************
changed: [home-wiseflat]

TASK [Start microservice] ********************************************************************************************
changed: [home-wiseflat]

TASK [Sleep 20 seconds] **********************************************************************************************
ok: [home-wiseflat -> localhost]

TASK [Copy your authorized_keys to home-cms] *************************************************************************
changed: [home-wiseflat]

PLAY RECAP ***********************************************************************************************************
home-wiseflat              : ok=42   changed=12   unreachable=0    failed=0
localhost                  : ok=13   changed=2    unreachable=0    failed=0

```


## User account

When installing the server, a token associated to your public IP is created automatically. This token is used to access [our online API](https://api.wiseflat.com).

If you don't make a request on our API for a month, your token (and their associated data) will be deleted.

### Creating a user account

You can have multiple tokens if you want:

- One for your raspberry pi
- One for your local area network, your home
- One for each of your raspberry pi

We will see later how to configure your Ansible project depending on your needs.

To create a new one:

- Delete variables from the env/group_vars/all.yml file
- Run the following playbook:

```sh
$ Ansible-playbook play/user.yml
```

### Updating user account information

If you've changed your user preferences by editing the env/group_vars/all.yml file, just run the same playbook 

```sh
$ Ansible-playbook play/user.yml
```

### Getting user account information

If you want to check your account information, run this playbook

```sh
Ansible-playbook play/user.yml
```
or 

```sh
$ Ansible-playbook play/tasks/api-user-info.yml
```

## Updating / upgrading

To keep your servers in safe and secure conditions, they need to be updated. 

### Raspberrypi updates

Unattended upgrades are enabled during the installation process. 

To keep the wiseflat-project up to date, update your git local project.

```sh
$ cd ~/wiseflat-project
$ git pull
```

Just run the server-install.yml playbook another time.

```sh
$ Ansible-playbook server-install.yml
``` 


### Microservice updates

Unattended upgrades are enabled in our pre-installed LXC images.

Your apps and their data will not be updated. You need to follow the official documentation of the application installed on your Linux container.


## Microservices

A microservice is a small application running into a Linux container.

There are two ways to add a new microservice:

- downloading it from our image repository
- or by creating yourself a new LXC container

To see a list of all the microservices available on our image repository, let's see what are the microservices available

```sh
cd ~/wiseflat-project
$ Ansible-playbook play/tasks/api-microservice-list.yml

PLAY [localhost] *****************************************************************************************************

TASK [Gathering Facts] ***********************************************************************************************
ok: [localhost]

TASK [uri] ***********************************************************************************************************
ok: [localhost]

TASK [debug] *********************************************************************************************************
ok: [localhost] => {
    "msg": {
        "message": "done",
        "status": "success",
        "value": [
            {
                "description": "Your personnal totaljs cms microservice",
                "label": "cms"
            },
            {
                "description": "Your personnal jeedom microservice",
                "label": "jeedom"
            },
            {
                "description": "Your personnal totaljs messenger microservice",
                "label": "messenger"
            },
            {
                "description": "Your personnal totaljs wiki microservice",
                "label": "wiki"
            },
            {
                "description": "Your personnal totaljs blog microservice",
                "label": "blog"
            }
        ]
    }
}

PLAY RECAP ***********************************************************************************************************
localhost                  : ok=3    changed=0    unreachable=0    failed=0

```

### Automatic deployment

If you want to add a new microservice from our image repository

```sh
cd ~/wiseflat-project
$ Ansible-playbook play/microservice-add.yml

PLAY [localhost] *****************************************************************************************************

TASK [Gathering Facts] ***********************************************************************************************
ok: [localhost]

TASK [uri] ***********************************************************************************************************
ok: [localhost]

TASK [debug] *********************************************************************************************************
ok: [localhost] => {
    "msg": {
        "message": "done",
        "status": "success",
        "value": [
            {
                "description": "Your personnal jeedom microservice",
                "label": "jeedom"
            },
            {
                "description": "Your personnal totaljs messenger microservice",
                "label": "messenger"
            },
            {
                "description": "Your personnal totaljs wiki microservice",
                "label": "wiki"
            },
            {
                "description": "Your personnal totaljs blog microservice",
                "label": "blog"
            },
            {
                "description": "Your personnal totaljs cms microservice",
                "label": "cms"
            }
        ]
    }
}
microservice name please : blog

PLAY [server] ********************************************************************************************************

TASK [Gathering Facts] ***********************************************************************************************
ok: [home-wiseflat]

TASK [clean up downloaded files] *************************************************************************************
ok: [home-wiseflat]

TASK [download microservice] *****************************************************************************************
changed: [home-wiseflat]

TASK [Delete old lxc image if exists] ********************************************************************************
changed: [home-wiseflat]

TASK [import microservice to lxd image store] ************************************************************************
changed: [home-wiseflat]

TASK [clean up downloaded files] *************************************************************************************
changed: [home-wiseflat]

TASK [create microservice] *******************************************************************************************
changed: [home-wiseflat]

TASK [start microservice] ********************************************************************************************
changed: [home-wiseflat]

TASK [copy your authorized_keys to home-test] ********************************************************************************************
changed: [home-wiseflat]

PLAY RECAP ***********************************************************************************************************
home-wiseflat              : ok=8    changed=6    unreachable=0    failed=0
localhost                  : ok=3    changed=0    unreachable=0    failed=0

```

Once the lxc container is deployed, your personal SSH public key is pushed into the container. You can quickly check what's inside by logging in through ssh:

```sh
$ ssh -F ssh/config home-blog
```

Open your browser and go to http://home-blog

### Manual deployment

Logging into your raspberrypi

```sh
$ ssh -F ssh/config home-wiseflat
```

Displaying the list of your Linux containers

```sh
pi@home-wiseflat:~ $ lxc list
+----------------+---------+------------------------+------+------------+-----------+
|      NAME      |  STATE  |          IPV4          | IPV6 |    TYPE    | SNAPSHOTS |
+----------------+---------+------------------------+------+------------+-----------+
| home-cms       | RUNNING | 192.168.1.192 (eth0)   |      | PERSISTENT | 0         |
+----------------+---------+------------------------+------+------------+-----------+
| home-jeedom    | RUNNING | 192.168.1.138 (eth0)   |      | PERSISTENT | 0         |
+----------------+---------+------------------------+------+------------+-----------+
| home-messenger | RUNNING | 192.168.1.124 (eth0)   |      | PERSISTENT | 0         |
+----------------+---------+------------------------+------+------------+-----------+
| home-wiki      | RUNNING | 192.168.1.197 (eth0)   |      | PERSISTENT | 0         |
+----------------+---------+------------------------+------+------------+-----------+
```

Creating a container

```sh
pi@home-wiseflat:~ $ lxc launch ubuntu:16.04 home-test
Creating home-test
Starting home-test

pi@home-wiseflat:~ $ lxc list home-test
+-----------+---------+------------------------+------+------------+-----------+
|   NAME    |  STATE  |          IPV4          | IPV6 |    TYPE    | SNAPSHOTS |
+-----------+---------+------------------------+------+------------+-----------+
| home-test | RUNNING | 192.168.1.230 (eth0)   |      | PERSISTENT | 0         |
+-----------+---------+------------------------+------+------------+-----------+
```

[See the official lxc documentation](https://linuxcontainers.org/fr/lxd/getting-started-cli/##creating-and-using-your-first-container)

Copying your ssh public key to this new container

```sh
pi@home-wiseflat:~ $ lxc file push ~/.ssh/authorized_keys home-test/home/ubuntu/.ssh/authorized_keys
```

Going back to your Ansible project and trying to log in

```sh
$ ssh -F ssh/config home-test
ubuntu@home-test:~$
```

### Deleting a microservice

A playbook is available to delete a microservice.

```sh
cd ~/wiseflat-project
$ Ansible-playbook play/microservice-delete.yml

PLAY [server] ********************************************************************************************************

TASK [Gathering Facts] ***********************************************************************************************
ok: [home-wiseflat]

TASK [List of your microservices] ************************************************************************************
changed: [home-wiseflat]

TASK [debug] *********************************************************************************************************
ok: [home-wiseflat] => {
    "msg": [
        "+----------+---------+------------------------+------+------------+-----------+",
        "|   NAME   |  STATE  |          IPV4          | IPV6 |    TYPE    | SNAPSHOTS |",
        "+----------+---------+------------------------+------+------------+-----------+",
        "| home-cms | RUNNING | 192.168.1.192 (eth0)   |      | PERSISTENT | 0         |",
        "+----------+---------+------------------------+------+------------+-----------+"
    ]
}
microservice name please : home-cms

PLAY [server] ********************************************************************************************************

TASK [Gathering Facts] ***********************************************************************************************
ok: [home-wiseflat]

TASK [Delete microservice] *******************************************************************************************
changed: [home-wiseflat]

TASK [debug] *********************************************************************************************************
ok: [home-wiseflat] => {
    "msg": "Please remove this host from your inventory"
}

PLAY RECAP ***********************************************************************************************************
home-wiseflat              : ok=6    changed=2    unreachable=0    failed=0

```

### Backing up a microservice

Your Linux containers are easy to back up. A LXC image will be created and copied to your Ansible project

```sh
$ cd ~/wiseflat-project
$ Ansible-playbook play/microservice-backup.yml

PLAY [server] ********************************************************************************************************

TASK [Gathering Facts] ***********************************************************************************************
ok: [home-wiseflat]

TASK [List of your microservices] ************************************************************************************
changed: [home-wiseflat]

TASK [debug] *********************************************************************************************************
ok: [home-wiseflat] => {
    "msg": [
        "+----------+---------+------------------------+------+------------+-----------+",
        "|   NAME   |  STATE  |          IPV4          | IPV6 |    TYPE    | SNAPSHOTS |",
        "+----------+---------+------------------------+------+------------+-----------+",
        "| home-cms | RUNNING | 192.168.1.192 (eth0)   |      | PERSISTENT | 0         |",
        "+----------+---------+------------------------+------+------------+-----------+"
    ]
}
microservice name please : home-cms

PLAY [server] ********************************************************************************************************

TASK [Gathering Facts] ***********************************************************************************************
ok: [home-wiseflat]

TASK [pwd] ***********************************************************************************************************
changed: [home-wiseflat -> localhost]

TASK [set_fact] ******************************************************************************************************
ok: [home-wiseflat]

TASK [Create backup directory] ***************************************************************************************
ok: [home-wiseflat -> localhost]

TASK [Stop microservice] *********************************************************************************************
changed: [home-wiseflat]

TASK [Publish lxc container as an image] *****************************************************************************
changed: [home-wiseflat]

TASK [Export lxc image] **********************************************************************************************
changed: [home-wiseflat]

TASK [Delete lxc image] **********************************************************************************************
changed: [home-wiseflat]

TASK [Start lxc container] *******************************************************************************************
changed: [home-wiseflat]

TASK [fetch image] ***************************************************************************************************
changed: [home-wiseflat]

PLAY RECAP ***********************************************************************************************************
home-wiseflat              : ok=13   changed=8    unreachable=0    failed=0
```

Checking your backup directory

```sh
$ ls -al backup
total 499712
drwxr-xr-x   3 me  me         96 20 avr 11:59 .
drwxr-xr-x  15 me  me        480 20 avr 11:31 ..
-rw-r--r--   1 me  me  254699066 20 avr 12:00 home-cms.tar.gz
```



## Issues

If you have any issues or questions, please report them on the wiseflat-project repository:

<a href="https://github.com/wiseflat/wiseflat-project/issues/new"><button class="btn btn-primary" type="submit">Report Issue</button></a>
<a href="https://github.com/wiseflat/wiseflat-project/issues"><button class="btn btn-primary" type="submit">Active Issues</button></a>

