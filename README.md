## Automated ELK Stack Deployment
This document contains the following details:


The files in this repository were used to configure the network depicted below.

Elk Stack Topology ![Project diagram1](https://user-images.githubusercontent.com/84750781/120741114-732d9a00-c4c2-11eb-8105-e6d68fbb40f4.png)



These files have been tested and used to generate a live ELK deployment on Azure. They can be used to either recreate the entire deployment pictured above. Alternatively, select portions of the Damn file may be used to install only certain pieces of it, such as Filebeat.

  -: Enter the playbook file._
  ansible-playbook install_elk.yml elk

This document contains the following details:
- Description of the Topologu
- Access Policies
- ELK Configuration
  - Beats in Use
  - Machines Being Monitored
- How to Use the Ansible Build


### Description of the Topology

The main purpose of this network is to expose a load-balanced and monitored instance of DVWA, the Vulnerable Web Application.

Load balancing ensures that the application will be highly available, in addition to restricting inbound access to the network.
- The load balancer ensures that work to process incoming traffic will be shared by both vulnerable web servers. Access controls will ensure that only authorized users —Web-1 and Web-2, ourselves — will be able to connect in the first place.

Integrating an ELK server allows users to easily monitor the vulnerable VMs for changes to the file system of the VMs onthe network as well as watch system metrics, such as CPU usage; attempted SSH logins; sudo escalation failures; etc.

- What does Filebeat watch for?_
- * Filebeat: Filebeat detects changes to the filesystem. Specifically, we use it to collect Apache logs.


- What does Metricbeat record?_
- *  Metricbeat: Metricbeat detects changes in system metrics, such as CPU usage. We use it to detect SSH login attempts, failed sudo escalations, and CPU/RAM statistics. 

The configuration details of each machine may be found below.

| Name     | Function    | IP Address | Operating System |
|----------|----------   |------------|------------------|
| Jump Box |  Gateway    | 10.0.0.4   | Linux            |
| DVWA 1   |  Web Server | 10.0.0.5   | Linux            |
| DVWA 2   |  Web Server | 10.0.0.6   | Linux            |
| ELK      |  Monitoring | 10.0.0.7   | Linux            |

### Access Policies

The machines on the internal network are not exposed to the public Internet. 

Only the jump box machine can accept connections from the Internet. Access to this machine is only allowed from the following IP addresses:
- 40.78.129.161

Machines within the network can only be accessed by each other.The DVWA 1 and DVWA 2 VMs send traffic to the ELK server.
- : Which machine did you allow to access your ELK VM? What was its IP address?_
-Ansible 10.0.0.6

A summary of the access policies in place can be found in the table below.

| Name     | Publicly Accessible | Allowed IP Addresses |
|----------|---------------------|----------------------|
| Jump Box | Yes                 | 40.78.129.161        |
| ELK      | No                  | 10.0.0.1-254         |
| DVWA 1   | No                  | 10.0.0.1-254         |
| DVWA 2   | No                  | 10.0.0.1-254         |

In addition to the above, Azure has provisioned a load balancer in front of all machines except for the jump box. The load balancer's targets are organized into the following availability zones:


Availability Zone 1: DVWA 1 + DVWA 2

Availability Zone 2: ELK

### Elk Configuration

Ansible was used to automate configuration of the ELK machine. No configuration was performed manually, which is advantageous because...
- : What is the main advantage of automating configuration with Ansible?_
- * Ansible is a simple automation language that can perfectly describe an IT application infrastructure. Ansible lets you model even highly complex IT workflows also it is felxible.


The playbook implements the following tasks:
- _TODO: In 3-5 bullets, explain the steps of the ELK installation play. E.g., install Docker; download image; etc._
- ...Install Docker contaner in Ansible
- ...Download image
- ...Nano a filebeat Playbook
- ...Run the Ansible Playbook filebeat.yml

<img width="1093" alt="Docker io" src="https://user-images.githubusercontent.com/84750781/120958716-4679bc80-c726-11eb-816c-62bf8f38cb7b.png">
The following screenshot displays the result of running `docker ps` after successfully configuring the ELK instance.

![TODO: Update the path with the name of your screenshot of docker ps output](<![image](https://user-images.githubusercontent.com/84750781/120958773-64dfb800-c726-11eb-92c9-e151858437ce.png)
>)

### Target Machines & Beats
This ELK server is configured to monitor the following machines:
- List the IP addresses of the machines you are monitoring_
* This ELK server is configured to monitor the DVWA 1 and DVWA 2 VMs, at 10.0.0.5 and 10.0.0.6, respectively.

We have installed the following Beats on these machines:
- : Specify which Beats you successfully installed_
* Filebeat and Metricbeat

These Beats allow us to collect the following information from each machine:
- : In 1-2 sentences, explain what kind of data each beat collects, and provide 1 example of what you expect to see. E.g., `Winlogbeat` collects Windows logs, which we use to track user logon events, etc._
- * Packetbeat: Packetbeat collects packets that pass through the NIC, similar to Wireshark. We use it to generate a trace of all activity that takes place on the network, in case later forensic analysis should be warranted.
- * Filebeat: Collects log events, and forwards them either to Elastisearch or Logstash for indexing.
- * Metricbeat: Collects metrics from the operating system and from services running on the server.

* The playbook below installs Metricbeat on the target hosts. The playbook for installing Filebeat is not included, but looks essentially identical — simply replace metricbeat with filebeat, and it will work as expected.
 name: Install metric beat
  hosts: webservers
  become: true
  tasks:
    # Use command module
  - name: Download metricbeat
    command: curl -L -O https://artifacts.elastic.co/downloads/beats/metricbeat/metricbeat-7.4.0-amd64.deb

    # Use command module
  - name: install metricbeat
    command: dpkg -i metricbeat-7.4.0-amd64.deb

    # Use copy module
  - name: drop in metricbeat config
    copy:
      src: /etc/ansible/files/metricbeat-config.yml
      dest: /etc/metricbeat/metricbeat.yml

    # Use command module
  - name: enable and configure docker module for metric beat
    command: metricbeat modules enable docker

    # Use command module
  - name: setup metric beat
    command: metricbeat setup

    # Use command module
  - name: start metric beat
    command: service metricbeat start


### Using the Playbook
In order to use the playbook, you will need to have an Ansible control node already configured. Assuming you have such a control node provisioned: 

SSH into the control node and follow the steps below:
- Copy the playbooks file to Ansible Control Node.
- Update the 
 ---
 - name: Filebeat Playbook
   hosts: webservers
   become: true
   tasks:
   - name: Download Filebeat deb file
     get_url:
       url: https:// artifacts.elastics.co/downloads/beats/filebeat/filebeat-7.6.1-amd64.deb
       dest: /home/jaasemrahman/filebeat.deb
   - name: Install Filebeat deb
     apt:
       deb: /home/jaasemrahman/filebeat.deb
   - name: Copy Configuration
     copy:
       src: /etc/ansible/files/filebeat-config.yml
       dest: etc/filebeat/filebeat.yml
   - name: Enable and Configure System Module
     command: filebeat modules enable system
   - name: Filebeat Setup
     command: filebeat setup
   - name: Start Filebeat
     service: filebeat start yes  
     
- Run the playbook, and navigate to Kibana, systemLogs to check that the installation worked as expected.

_: Answer the following questions to fill in the blanks:_
- _Which file is the playbook?
- * hosts file
- _Which file do you update to make Ansible run the playbook on a specific machine? 
- Update Ansible Control Node
- How do I specify which machine to install the ELK server on versus which to install Filebeat on?_
- You must create a hosts file to specify which VMs to run each playbook on. Run the commands below:
$ cd /etc/ansible
$ cat > hosts <<EOF
[webservers]
10.0.0.5
10.0.0.6

[elk]
10.0.0.8
EOF
- Where filebeat is installed using Ansible docker container
- Elk
- _Which URL do you navigate to in order to check that the ELK server is running?
- curl http://10.0.0.8:5601

_As a **Bonus**, provide the specific commands the user will need to run to download the playbook, update the files, etc._
                    
-$ cd /etc/ansible
                    
$ ansible-playbook install_elk.yml elk
                    
$ ansible-playbook install_filebeat.yml webservers
                    
$ ansible-playbook install_metricbeat.yml webservers
