## Cloud Scripts
Ansible Scripts from my CyberClass

### Automated ELK Stack Deployment

The files in this repository were used to configure the network depicted below.

![elk-project-diagram.png](/diagrams/elk-project-diagram.png)

These files have been tested and used to generate a live ELK deployment on Azure. They can be used to either recreate the entire deployment pictured above. 
Alternatively, select portions of the "playbook" file may be used to install only certain pieces of it, such as Filebeat.

![pentest.yml](/ansible/pentest.yml)

![install-elk.yml](/ansible/install-elk.yml)

![filebeat-playbook.yml](/ansible/filebeat-playbook.yml)

![filebeat-config.yml](/ansible/filebeat-config.yml)

![metricbeat-playbook.yml](/ansible/metricbeat-playbook.yml)

![metricbeat-config.yml](/ansible/metricbeat-config.yml)


This document contains the following details:
- Description of the Topology
- Access Policies
- ELK Configuration
  - Beats in Use
  - Machines Being Monitored
- How to Use the Ansible Build


### Description of the Topology

The main purpose of this network is to expose a load-balanced and monitored instance of DVWA, the "Damn Vulnerable Web Application".

Load balancing ensures that the application will be highly available, in addition to restricting inbound access to the network.
The load balancer ensures that work to process incoming traffic will be shared by all vulnerable web servers. Access controls will ensure that only 
authorized users — namely, ourselves — will be able to connect in the first place.
The jump box sits in front of other machines and controls access to the other machines by allowing connections from specific IP addresses and forwarding to those machines.

Integrating an ELK server allows users to easily monitor the vulnerable VMs for changes to the file systems of the VMs on the network, as well as watch system metrics,
such as CPU usage; attempted SSH logins; sudo escalation failures; etc.
Filebeat monitors changes to the file systems, while Metricbeat records system metrics.

The configuration details of each machine may be found below.

| Name     | Function | IP Address | Operating System |
|----------|----------|------------|------------------|
| Jump Box | Gateway  | 10.0.0.4   | Linux            |
| DVWA1    | WebServer| 10.0.0.5   | Linux            |
| DVWA2    | WebServer| 10.0.0.6   | Linux            |
| DVWA3    | WebServer| 10.0.0.7   | Linux            |
| ELK      | Monitor  | 10.1.0.4   | Linux            |

In addition to the above, Azure has provisioned a load balancer in front of all web servers. 
The load balancer's targets are organized into the following availability zones:

Availability Zone 1: DVWA1
Availability Zone 2: DVWA2
Availability Zone 3: DVWA3


### Access Policies

The machines on the internal network are not exposed to the public Internet. 

Only the jump box machine can accept connections from the Internet. Access to this machine is only allowed from the following IP addresses: 47.156.12.41

Machines within the network can only be accessed from the joump box via SSH, and via firewall via load balancer by HTTP (port 80) only. 
The DVWA1, DVWA2, and DVWA3 VMs send traffic to the ELK server.

A summary of the access policies in place can be found in the table below.

| Name     | Publicly Accessible | Allowed IP Addresses |
|----------|---------------------|----------------------|
| Jump Box | Yes                 | 47.156.12.41         |
| ELK      | No                  | 10.0.0.1-254         |
| DVWA1    | No  HTTP (port 80)  | 10.0.0.1-254         |
| DVWA2    | No  HTTP (port 80)  | 10.0.0.1-254         |
| DVWA3    | No  HTTP (port 80)  | 10.0.0.1-254         |


### Elk Configuration

The ELK VM exposes an Elastic Stack instance. Docker is used to download and manage an ELK container.
Rather than configure ELK manually, we opted to develop a reusable Ansible Playbook to accomplish the task.
 
This playbook is duplicated below.
![install-elk.yml](/ansible/install-elk.yml)
To use this playbook, one must log into the Jump Box, then issue: ansible-playbook install_elk.yml elk. 
This runs the install_elk.yml playbook on the elk host.

When ELK installs we run:
sysadmin@elk:~$ sudo docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                                                                              NAMES
842caa422ed8        sebp/elk            "/usr/local/bin/star…"   3 hours ago         Up 7 seconds          0.0.0.0:5044->5044/tcp, 0.0.0.0:5601->5601/tcp, 0.0.0.0:9200->9200/tcp, 9300/tcp   elk
sysadmin@elk:~$


### Target Machines & Beats
This ELK server is configured to monitor the DVWA1, DVWA2, and DVWA3 VMs, at 10.0.0.5, 10.0.0.6, and 10.0.0.7 respectively.
We have installed the following Beats on these machines:

Filebeat
Metricbeat

These Beats allow us to collect the following information from each machine:

Filebeat: Filebeat detects changes to the filesystem. Specifically, we use it to collect Apache logs.
Metricbeat: Metricbeat detects changes in system metrics, such as CPU usage. We use it to detect SSH login attempts, failed sudo escalations, and CPU/RAM statistics.
The playbooks below install Filebeat and Metricbeat on the target hosts. 
![filebeat-playbook.yml](/ansible/filebeat-playbook.yml)
![metricbeat-playbook.yml](/ansible/metricbeat-playbook.yml)


### Using the Playbook
In order to use the playbook, you will need to have an Ansible control node already configured. Assuming you have such a control node provisioned: 

SSH into the control node and follow the steps below:
- Copy the the playbook file to the Ansible Control Node.
- Update the playbook file with correct information.
- Next, you must create a hosts file to specify which VMs to run each playbook on. Run the commands below:
$ cd /etc/ansible

- Next, update entries in the hosts file:
[webservers]
10.0.0.5
10.0.0.6
10.0.0.7

[elk]
10.1.0.4

After this, the commands below run the playbook:
$ cd /etc/ansible
$ ansible-playbook install_elk.yml elk
$ ansible-playbook install_filebeat.yml webservers
$ ansible-playbook install_metricbeat.yml webservers

To verify success, run: curl http://10.1.0.4:5601. 
This is the address of Kibana. If the installation succeeded, this command should print HTML to the console.