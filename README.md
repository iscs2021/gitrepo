## Automated ELK Stack Deployment

The files in this repository were used to configure the network depicted below.

[Cloud-VirtualNetwork-Web-Testing-Diagram.png](Diagrams/Cloud-VirtualNetwork-Web-Testing-Diagram.png)

These files have been tested and used to generate a live [ELK](https://www.elastic.co/what-is/elk-stack) deployment on [Azure](https://portal.azure.com). They can be used to either recreate the entire deployment pictured above. Alternatively, select portions of the **[ansible](https://www.ansible.com/overview/how-ansible-works)** playbook file may be used to install only certain pieces of it, such as Filebeat.

  - [configvm.yaml](Ansible/roles/configvm.yaml)

This document contains the following details:
- [Description of the Topology](#description-of-the-topology)
- [Access Policies](#access-policies)
- [ELK Configuration](#elk-configuration)
  - [Beats in Use](#beats-in-use)
  - [Machines Being Monitored](#machines-being-monitored)
- [How to Use the Ansible Build](#how-to-use-the-ansible-build)


### Description of the Topology

The main purpose of this network is to expose a load-balanced and monitored instance of **[DVWA, the D*mn Vulnerable Web Application](https://khannasecurity.com/blog/what-is-dvwa-and-why-ethical-hacker-love-this/)**.

Load balancing ensures that the application will be highly available, in addition to restricting access to the network.
- What aspect of security do load balancers protect?  The [CIA triad](https://www.forcepoint.com/cyber-edu/cia-triad) consists of confidentiality, integrity, and availability. The aspect of security that load balancers protect is Availability. 
- What is the advantage of a jump box?  The advantage of a jump box is it limits the exposure of, in this case, a virtual network to the public such that authorized access can occur only through the use of a secure connection via [encryption](https://www.101computing.net/symmetric-vs-asymmetric-encryption/) keys.

Integrating an ELK server allows users to easily monitor the vulnerable VMs for changes to the **file system logs** and system **metrics**.
- What does Filebeat watch for?  Filebeat watches for changes to specific system logs and application logs
- What does Metricbeat record?  Metricbeat records Operating System performance metrics, such as CPU usage, or memory (RAM) usage.

The configuration details of each machine may be found in the table below.
Note: The following table was created using the [Markdown Table Generator](http://www.tablesgenerator.com/markdown_tables).

| Name          | Function     | IP Address | Operation System       |
|---------------|--------------|------------|------------------------|
| Jump Box      | gateway      | 10.0.0.4   | UbuntuServer 18.04-LTS |
| Web-1         | web server   | 10.0.0.5   | UbuntuServer 18.04-LTS |
| Web-2         | web server   | 10.0.0.6   | UbuntuServer 18.04-LTS |
| Web-3         | web server   | 10.0.0.7   | UbuntuServer 18.04-LTS |
| Load Balancer | availability | 10.0.0.8   | Azure resource         |
| ELKServer     | monitoring   | 10.1.0.4   | UbuntuServer 18.04-LTS |

### Access Policies

The machines on the internal network are not exposed to the public Internet. 

Only the jump box virtual machine can accept connections from the Internet. Access to this machine is only allowed from the following IP addresses:
- only my local home ip, which is subject to change

Machines within the network can only be accessed by SSH.
- Which machine did you allow to access your ELK VM?  Only the jump box is allowed to access the ELK VM 
- What was its (jump box's) IP address?  The IP address of the jump box is 10.0.0.4

A summary of the access policies in place can be found in the table below.
| Name          | Publicly Accessible | Allowed connection to IP Addresses | Allowed connection from IP Addresses |
|---------------|---------------------|------------------------------------|--------------------------------------|
| Jump Box      | Yes (ssh)           | 10.0.0.4                           | my local ip                          |
| Web-1         | No                  | 10.1.0.4                           | 10.0.0.4, 10.1.0.4                   |
| Web-2         | No                  | 10.1.0.4                           | 10.0.0.4, 10.1.0.4                   |
| Web-3         | No                  | 10.1.0.4                           | 10.0.0.4, 10.1.0.4                   |
| Load Balancer | No                  | 10.0.0.5, 10.0.0.6, 10.0.0.7       | my local ip                          |
| ELKServer     | No                  | 10.0.0.5, 10.0.0.6, 10.0.0.7       | my local ip (http), jump box (ssh)   |

### ELK Configuration

Ansible was used to automate configuration of the ELK machine. No configuration was performed manually, which is advantageous because...
- What is the main advantage of automating configuration with Ansible?  The main advantage of automating configuration with Ansible is we can change the configuration at one central location which changes many devices or machines or virtual machines remotely, i.e. **it saves time**.  Such time previously would have been spent looking up machine names or addresses and login credentials, then logging on, then running commanands, then logging off. then looking up the next machine and rinse and repeat.

The [install-elk.yaml](Ansible/roles/install-elk.yaml) playbook implements the following tasks:
- install docker
- install python3-pip
- install docker module from pip
- enable the docker service
- configure virtual memory
- download and launch the elk docker_container

The following screenshot displays the result of running `docker ps` after successfully configuring the ELK instance.

![screenshot of docker ps output](Images/activity-13-1-docker-ps-sebp-elk.JPG)

### Target Machines & Beats

### Beats in Use
We have installed the following Beats on these machines:
- [Filebeat](https://www.elastic.co/guide/en/beats/filebeat/current/filebeat-overview.html) and [Metricbeat](https://www.elastic.co/guide/en/beats/metricbeat/current/metricbeat-overview.html)

These Beats allow us to collect the following information from each [machine](#machines-being-monitored):
- Filebeat monitors specific logs files for system and services, wherein I expect to see systemd, containerd, kernal, pythond log changes.
- Metricbeat monitors system performance info, wherein I expect to see CPU usage, memory usage, number of containers running.

### Machines Being Monitored
This ELK server is configured to monitor the following machines:
- 10.0.0.5, 10.0.0.6, 10.0.0.7

### How to Use the Ansible Build
In order to use the playbook for an Ansible Build, you will need to have an Ansible control node already configured. Assuming you have such a control node provisioned: 

SSH into the control node and follow the steps below:
- Copy the files [filebeat-config.yml](Ansible/files/filebeat-config.yml) and [metricbeat-config.yml](Ansible/files/metricbeat-config.yml) to /etc/ansible/files .
- Update the filebeat-config.yml and metricbeat-config.yml to include the __IP address__ and __port__ for elasticsearch and kibana. 
- Update the [filebeat-playbook.yml](Ansible/files/filebeat-playbook.yml) and [metricbeat-playbook.yml](Ansible/files/metricbeat-playbook.yml) such that the value after __src:__ is __/etc/ansible/files/filebeat-config.yml__ or __/etc/ansible/files/metricbeat-config.yml__ respectively.
- Run the playbook, and navigate to kibana filebeat syslog overview and kibana metricbeat docker overview to check that the installation worked as expected.

- [screenshot for successful filebeat install](Images/activity-13-2-filebeat-install-success.JPG)
- [screenshot for successful metricbeat install](Images/activity-13-2-metricbeat-install-success.JPG)

Additional questions answered:
- Which file is the playbook?  The playbooks are named `install-elk.yaml`, `filebeat-playbook.yml` and `metricbeat-playbook.yml`. 
- Where do you copy the playbook?  The playbooks are copied to `/etc/ansible/files` on the **docker container** on the **jumpbox**.
- Which file do you update to make Ansible run the playbook on a specific machine?  The `/etc/ansible/hosts` file is updated to run Ansible playbook on a specific machine.
- How do I specify which machine to install the ELK server on versus which to install Filebeat on?  
To specify on which machine to install the ELK server, edit the file `/etc/ansible/roles/install-elk.yaml` on the **docker container** on the **jumpbox**, modifying the value after **hosts:**.
To specify on which machine to install Filebeat, edit the file `/etc/ansible/files/filebeat-playbook.yml` on the **docker container** on the **jumpbox**, modifying the value after **hosts:**
- To which URL do you navigate in order to check that the ELK server is running?  To check that the ELK server is running, navigate to `http://10.1.0.4:5601`  Note: The IP address is subject to change.

As a **Bonus**, below I provide the specific commands the user will need to run to ssh to the jump box, run docker service, start a docker container, edit a playbook, run a playbook, download the ansible directory to the jump box, download the ansible directory to a local PC.
Assuming the user is on a local Windows PC, has GitBash installed, and has already copied SSL public keys to the appropriate places for access, and has used ssh to connect to the jump box, and suppose ansible can be run on a docker container named **fire_snow**

- SSH from PC to the jump box
```
Windows-PC-Gitbash> ssh MyUser@jump_box_IPAddress
```

- start the docker service
```
jump_box# sudo systemctl start docker
```

- list all containers
```
jump_box# sudo docker container list -a
```

- start a container named fire_snow
```
jump_box# sudo docker start fire_snow
```

- attach to fire_snow container
```
jump_box# sudo docker attach fire_snow
```

- change directory and edit the filebeat playbook then run the playbook
```
fire_snow# cd /etc/ansible
fire_snow# nano ./files/filebeat-playbook.yml
```

- run the filebeat playbook
```
fire_snow# ansible-playbook ./files/filebeat-playbook.yml
```

- change directory then create tar file of the ansible directory, then exit the container
```
fire_snow# cd /etc
fire_snow# tar cvf /tmp/ansible.tar ./ansible
fire_snow# exit
```

- use docker to download (copy) the tar file to the host
```
jump_box# sudo docker cp fire_snow:/tmp/ansible.tar ~
```

- stop the docker container, then the docker service, then exit the jump box
```
jump_box# sudo docker stop fire_snow
jump_box# sudo systemctl stop docker
jump_box# exit
```

- use scp (secure copy) to download the tar file from the jump box
```
Windows-PC-Gitbash> scp jump_box_IPaddress:/home/MyUser/ansible.tar .
```

- extract the tar file on the local pc
```
Windows-PC-Gitbash> tar xvf ./ansible.tar
```
