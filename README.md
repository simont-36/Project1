## Automated ELK Stack Deployment

The files in this repository were used to configure the network depicted in the file below.

[Images/Cloud_Security_Network_Diagram](./Images/Cloud_Security_Network_Diagram.pdf)

These files have been tested and used to create a load balanced instance of DVWA and to generate a live ELK deployment on Azure to monitor the associated servers. They can be used to either recreate the entire deployment or alternatively, used selectively to install only certain pieces of it, such as Filebeat.

  - filebeat-configuration.yml
  - filebeat-playbook.yml
  - install-elk.yml
  - metricbeat-config.yml
  - metricbeat-playbook.yml
  - pentest.yml

This document contains the following details:
- Description of the Topology
- Access Policies
- ELK Configuration
  - Beats in Use
  - Machines Being Monitored
- How to Use the Ansible Build


### Description of the Topology

The main purpose of this network is to expose a load-balanced and monitored instance of DVWA, the D*mn Vulnerable Web Application.

Load balancing ensures that the application will be highly available, in addition to restricting access to the network.
Load Balancers are able to protect against DDoS attacks by limiting attack traffic and also by having the possibility of offloading attack traffic to another possibly cloud based server.

A jump box sits in between the public internet and the rest of the machines in the network and helps to control access to the other servers particularly management/control access. Securing and monitoring this single node is much easier than trying to excersize the same level of control across each individual node in the network. It also simplifies routing and therefore makes design easier.

Integrating an ELK server allows users to easily monitor the vulnerable VMs for changes to the filesystem and system logs.
- Filebeat specifically collects data about the filesystem
- Metric beat collect machine metrics eg: uptime, cpu usage etc...

The configuration details of each machine may be found below.


| Name     | Function    | IP Address      | Operating System | Notes                                  |
|----------|-------------|-----------------|------------------|----------------------------------------|
| JumpBox  | Gateway     | 10.0.0.9        | Linux            | Internal                               |
| JumpBox  | Gateway     | 13.70.87.98     | Linux            | Accessible via SSH from whitelisted IP |
| RedWeb1  | Webserver   | 10.0.0.6        | Linux            | Management address                     |
| RedWeb2  | Webserver   | 10.0.0.7        | Linux            | Management address                     |
| RedWeb3  | Webserver   | 10.0.0.10       | Linux            | Management address                     |
| ELKServer| Monitoring  | 10.1.0.4        | Linux            | Management address                     |
| ELKServer| ELK Service | 13.73.105.55    | Linux            | Accessible from internet on port 5601  |
| RedLB    |Load Balancer| 104.210.105.184 | Linux            | Address used to access DVWA Webservers |

### Access Policies

The machines on the internal network are not exposed to the public Internet. 

Only the JumpBox machine can accept connections from the Internet. Access to this machine is only allowed from the following IP addresses:
-123.123.123.45 (IP Address of workstation)

Machines within the network can only be accessed by ssh from the ansible container located on the Jumpbox.
- Management access to the ELK server was only allowed by also using ssh from the ansible container on the jumpbox.

A summary of the access policies in place can be found in the table below.

| Name        | Publicly Accessible | Allowed IP Addresses |
|-------------|---------------------|----------------------|
| Jump Box    | Yes                 | 123.123.123.45        |
| RedWeb1     | No                  | 10.0.0.9             |
| RedWeb2     | No                  | 10.0.0.9             |
| RedWeb3     | No                  | 10.0.0.9             |
| ELKServer   | No                  | 10.0.0.9             |

### Elk Configuration

Ansible was used to automate configuration of the ELK machine. No configuration was performed manually, which is advantageous because...
- it is easily replicatable, reduces the possibility of error, easy to carry out tests and makes scalability easy. UPdates and bug fixes can also be rolled out very easily by editing the playbook.

The playbook implements the following tasks:

- Install docker
- Install python and pip3 
- Increase virtual memory
- Dowload and install docker ELK container
- Enable service docker on boot

The following screenshot displays the result of running `docker ps` after successfully configuring the ELK instance.

[Images/docker_ps_on_ELK_machine](.Images/docker_ps_on_ELK_machine.jpg)

### Target Machines & Beats
This ELK server is configured to monitor the following machines:
- RedWeb1 - 10.0.0.6
- RedWeb2 - 10.0.0.7
- RedWeb3 - 10.0.0.10

We have installed the following 'Beats' on these machines:
- filebeat
- metricbeat

These Beats allow us to collect the following information from each machine:
- Filebeat helps to generate and organise logfiles to send to logstash and elastic search. It logs changes to the filesystem collecting logs from specific files eg: those generated by Apache or MySQL databases.
- Metricbeat collects statistics on things like CPU useage, memory, file system, disk IO and network IO and more. It also collects metrics from services such as Apache, nGINX, MySQL etc...

### Using the Playbooks
In order to use the playbook, you will need to have an Ansible control node already configured. Assuming you have such a control node provisioned: 

SSH into the control node and follow the steps below:

To create the DVWA web app on the 3 webserver machines

- Establish an ssh connection to the JumpBox 
- start the docker service if not started already
- check the available containers
- start the ansible container if not started
- attach to the ansible container
- Copy the pentest.yml file to the /etc/ansible/ folder on the container.
- Update the /etc/hosts file to include the  ip addresses of the servers under the label webservers
- Update /etc/ansible/ansible.cfg to set the remote_user option.
- Run the playbook, and navigate to the VM's and run curl localhost/setup.php to check that the installation worked as expected. You should get some HTML back as a result.

To create the ELK stack on the VM for ELK

- first connect to the Jump Box and then to the ansible container as outlined above
- copy the install-elk.yml file to the etc/ansible folder
- Update the etc/hosts file to include the ip address of the ELK server under the label elk 
- Run the playbook.
- from the test workstation navigate to http://13.73.105.55:5601/app/kibana to check functionality

To set up filebeat and metricbeat on the DVWA servers

- first connect to the Jump Box and then to the ansible container as outlined above
- copy the filebeat-config.yml file to the etc/ansible folder
- ensure that the ip address for the ELK server is correct at lines 1106 & 1806.
- copy the filebeat-playbook.yml file to the etc/ansible/roles folder
- Run the playbook
- 
- Repeat the above steps to install metricbeat but using the metrticbeat files instead of the filebeat files.

- Navigate from the test workstation to the ELK server and verify that logs are being received. 

### Example commands

 - ssh RedTeam@13.70.87.98
 - sudo systemctl start docker
 - sudo docker container list
 - sudo docker start <container-name>
 - sudo docker attach <container-name>
 - ansible-playbook /etc/ansible/pentest.yml
 - ansible-playbook /etc/ansible/install-elk.yml
 - ansible-playbook /etc/ansible/roles/filebeat-playbook.yml
