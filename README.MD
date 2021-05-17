Automated ELK Stack Deployment
The files in this repository were used to configure the network depicted below.
![image](https://user-images.githubusercontent.com/78760503/118496655-53d8f380-b6f2-11eb-88f4-75dc5c648e2c.png)

These files have been tested and used to generate a live ELK deployment on Azure. They can be used to either recreate the entire deployment pictured above. Alternatively, select portions of the filebeat-playbook.yml file may be used to install only certain pieces of it, such as Filebeat.


This document contains the following details:

Description of the Topologu
Access Policies
ELK Configuration

Beats in Use
Machines Being Monitored


How to Use the Ansible Build


Description of the Topology
The main purpose of this network is to expose a load-balanced and monitored instance of DVWA, the D*mn Vulnerable Web Application.
Load balancing ensures that the application will be highly available in addition to restricting access to the network.

Load Balancers protect against distributed denial of service attacks, and ensure availabilty. The advantage of a jump box is a provide restricted access to your network enviroment

Integrating an ELK server allows users to easily monitor the vulnerable VMs for changes to the opsrating system and system services.

Filebeat monitors file logs, collects log events and forwards them for indexing.
Metricbeat records metrics and statistics from an operating system and services running on a server_

The configuration details of each machine may be found below.

| Name     | Function       | IP Address | Operating System |
|----------|----------------|------------|------------------|
| Jump Box | Gateway        | 10.0.0.4   | Linux            |
| Web-1    | DVWA Container | 10.0.0.9   | Linux            |
| Web-2    | DVWA Container | 10.0.0.10  | Linux            |
| Web-3    | DVWA Container | 10.0.0.11  | Linux            |





Access Policies
The machines on the internal network are not exposed to the public Internet.
Only the JumpBox machine can accept connections from the Internet. Access to this machine is only allowed from the following IP addresses:
72.226.31.61

Machines within the network can only be accessed by SSH.


The Jump Box was allowed access the ELK VM It's IP Address was 10.1.0.4 A summary of the access policies in place can be found in the table below.

| Name     | Publicly Accesible | Allowed IP Addresses |
|----------|--------------------|----------------------|
| Jump Box | Yes                | 72.226.31.61         |
| Web-1    | No                 | 10.0.0.4             |
| Web-2    | No                 | 10.0.0.4             |
| Web-3    | No                 | 10.0.0.4             |
| ELKVM    | Yes                | 72.226.31.61         |













Elk Configuration
Ansible was used to automate configuration of the ELK machine. No configuration was performed manually, which is advantageous because...it allows administrators to automate  their daily tasks. This gives them more time to work on more important tasks.
The playbook implements the following tasks:

Specify a different group of machines as well as a different remote user
- name: Config Elk Vm with Docker
  hosts: elk
  remote_user: dmalon
  become: true
  tasks:

Install docker.io, python3-pip, docker
    - name: Install docker.io
      apt:
        force_apt_get: yes
        update_cache: yes
        name: docker.io
        state: present
    - name: Install python3-pip
      apt:
        force_apt_get: yes
        name: python3-pip
        state: present

    - name: Install docker module
      pip:
        name: docker
        state: present
  


Download and Launch the ELK container with these published ports:

    - name: download and launch a docker elk container
      docker_container:
        name: elk
        image: sebp/elk:761
        state: started
        restart_policy: always
        published_ports:
          - 5601:5601
          - 9200:9200
          - 5044:5044

    - name: docker.service
      systemd:
        name: docker.service
        enabled: yes

       


...
...

The following screenshot displays the result of running docker ps after successfully configuring the ELK instance.

![image](https://user-images.githubusercontent.com/78760503/118497425-0f9a2300-b6f3-11eb-9ba9-5f8bd59299d2.png)



Target Machines & Beats
This ELK server is configured to monitor the following machines: Web1 10.0.0.9 Web2 10.0.0.10; Web3 10.0.0.11  
We have installed the following Beats on these machines:
Filebeat
These Beats allow us to collect the following information from each machine:

Filebeat generates and organizes log files to send to Elasticsearch on the ELK Server. The logs contain information about the file system such as which files have changed and when.


These Beats allow us to collect the following information from each machine:


Using the Playbook
In order to use the playbook, you will need to have an Ansible control node already configured. Assuming you have such a control node provisioned:
SSH into the control node and follow the steps below:
Copy the install-elk.yml file to Ansible container folder /etc/ansible/files/.

Update the hosts file to include the Elk Server IP address of 10.1.0.4 under elkservers

Run the playbook, ansible-playbook elk-playbook.yml /etc/ansible/http://20.94.215.86:5601 to check that Kibana is running.


