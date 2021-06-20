# Automated ELK Stack Deployment

The files in this repository were used to configure the network depicted below.

![Azure projrct diagram](https://user-images.githubusercontent.com/79946393/121992326-0ab6a680-cd67-11eb-884e-f6b3101ee05f.png)

These files have been tested and used to generate a live ELK deployment on Azure. They can be used to either recreate the entire deployment pictured above. Alternatively, select portions of the project file may be used to install only certain pieces of it, such as Filebeat.

[ELK]()
This document contains the following details:

- Description of the Topology
- Access Policies
- ELK Configuration
  - Beats in Use
  - Machines Being Monitored
- How to Use the Ansible Build

## Description of the Topology

The main purpose of this network is to expose a load-balanced and monitored instance of DVWA, the D*mn Vulnerable Web Application.

Load balancing ensures that the application will be highly responsive, in addition to restricting overload to the network.
-Load balancers protect against DoS and DDoS attacks and the reason for having a JumpBox was to prevent the machines in the network from being exposed to the public.

Integrating an ELK server allows users to easily monitor the vulnerable VMs for changes to the log and/or system traffic changes.

Filebeat watches for log files, collects them, and forwards them to Elasticsearch or Logstash.
Metricbeat records metric data from your system and services. i.e. CPU usage, memory, etc.

The configuration details of each machine may be found below.
_Note: Use the [Markdown Table Generator](http://www.tablesgenerator.com/markdown_tables) to add/remove values from the table_.

| Name                  | Function  | IP Address | Operating System |
|-----------------------|-----------|------------|------------------|
| Jump-Box-Provisioner  | Gateway   | 10.1.0.4   | Linux            |
| Web-1                 | Web-server| 10.1.0.7   | Linux            |
| Web-2                 | Web-server| 10.1.0.8   | Linux            |
| Web-3                 | Web-server| 10.1.0.9   | Linux            |
| ELK-server            | ELK-stack | 10.2.0.4   | Linux            |

## Access Policies

The machines on the internal network are not exposed to the public Internet. 

Only the JumpBox  machine can accept connections from the Internet. Access to this machine is only allowed from the following IP addresses:

- Personal public IP address
  12.345.678.901

Machines within the network can only be accessed by ssh.

-The ELK server only allows my Personal IP address to access the machine while using a specific port.
 A summary of the access policies in place can be found in the table below.

| Name                 | Publicly Accessible | Allowed IP Addresses |
|----------------------|---------------------|----------------------|
| Jump-Box-Provisioner | Yes                 | xx.xxx.xxx.xxx             |
| Web-1                | No                  | 10.1.0.7             |
| Web-2                | No                  | 10.1.0.8             |
| Web-3                | No                  | 10.1.0.9             |
| ELK-server           | No                  | Personal IP Address  |

Note that none of the machines were accessible from the public for this project.

## Elk Configuration

Ansible was used to automate configuration of the ELK machine. No configuration was performed manually, which is advantageous because…

- Ansible's automatic configuration is quick and simple to set up and use.

The playbook implements the following tasks:

- Install Docker
Install python3-pip
Install Docker module pip
Use systemctl to use more memory
Download and launch Docker container sebp/elk:761
The following screenshot displays the result of running `docker ps -a` after successfully configuring the ELK instance.

## Target Machines & Beats

This ELK server is configured to monitor the following machines:

Web-1, 10.1.0.7
Web-2, 10.1.0.8
Web-3, 10.1.0.9

We have installed the following Beats on these machines:

Filebeats
Metricbeats

These Beats allow us to collect the following information from each machine:

Filebeat focuses on tracking system events by gathering system logs.
Metricbeat focuses on monitoring the usage of resources by collecting system and service metric data.

### Using the Playbook

In order to use the playbook, you will need to have an Ansible control node already configured. Assuming you have such a control node provisioned: 

SSH into the control node and follow the steps below:
Copy the filebeat and metricbeat config files to /etc/ansible/[your_dir]/[your-file-name].
Update the config files to include your ELK-server's private IP address. (filebeat-config.yml: lines 1106 & 1806, metricbeat-config.yml: lines 62 & 96)
Run the playbook, and navigate to http://[ELK-server-public-IP]:[your port]/app/kibana to check that the installation worked as expected.
/etc/ansible/roles/filebeat-playbook.yml is the filebeat playbook. Copy it to /etc/ansible/[your_dir]/[your-playbook-name]
/etc/ansible/files/metricbeat-playbook.yml is the metricbeat playbook. Copy it to /etc/ansible/[your_dir]/[your-playbook-name]
To make Ansible run the playbook on a specific machine(s), update the /etc/ansible/hosts file. Within this file, name groups using square brackets (currently contains '[webservers]' and '[elk]') surrounding the group-name and under the group-name, replace private IP's with your own respective IP's. You then use these group-names behind the "hosts:" field in the playbooks to specify which machines to apply the playbook to.
To confirm the ELK-server is running go to http://[ELK-server-public-IP:[your port]/app/kibana

_As a **Bonus**, provide the specific commands the user will need to run to download the playbook, update the files, etc._

To create the filebeat-configuration.yml file: nano filebeat-configuration.yml. 
For this, I used the filebeat configuration file template.
To create the playbook: nano filebeat-playbook.yml

name: installing and launching filebeat hosts: webservers become: true tasks:

- name: download filebeat deb
  command: curl -L -O <https://artifacts.elastic.co/downloads/beats/filebeat/filebeat-7.7.1-amd64.deb>

- name: install filebeat deb
  command: dpkg -i filebeat-7.7.1-amd64.deb

- name: drop in filebeat.yml
  copy:
    src: ./files/filebeat-configuration.yml
    dest: /etc/filebeat/filebeat.yml

- name: enable and configure system module
  command: filebeat modules enable system

- name: setup filebeat
  command: filebeat setup

- name: start filebeat service
 command: service filebeat start

-To run the playbook: ansible-playbook filebeat-playbook.yml
In order to run the playbook, you have to be in the directory the playbook is at, or give the path to it (ansible-playbook /etc/ansible/roles/filebeat-playbook.yml
-------Metricbeat-------
To create the metricbeat-configuration.yml file: nano metricbeat-configuration.yml. For this, I used the metricbeat configuration file template.
To create the playbook: nano metricbeat-playbook.yml

name: installing and lunching metricbeat hosts: webservers become: true tasks:
name: download metricbeat deb command: curl -L -O <https://artifacts.elastic.co/downloads/beats/metricbeat/metricbeat-7.7.1-amd64.deb>
name: install metricbeat deb command: sudo dpkg -i metricbeat-7.7.1-amd64.deb
name: drop in metricbeat.yml copy: src: /etc/ansible/roles/files/metricbeat-configuration.yml dest: /etc/metricbeat/metricbeat.yml
name: enable and configure system module command: metricbeat modules enable system
name: setup metricbeat command: metricbeat setup
name: start metricbeat service command: service metricbeat start

To run the playbook: ansible-playbook metricbeat-playbook.yml
To order to run the playbook, you have to be in the directory the playbook is at, or give the path to it (ansible-playbook /etc/ansible/roles/metricbeat-p
