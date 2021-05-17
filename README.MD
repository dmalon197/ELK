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
Specify a different group of machines as well as a different remote use:

      - name: Config Elk Vm with Docker
        hosts: elk
        remote_user: dmalon
        become: true
        tasks:
Install docker.io, python3-pip, docker:
    
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
Increase system memory:
     
    - name: Increase virtual memory
      command: sysctl -w vm.max_map_count=262144

     - name: vm.max_map_count
       sysctl:
         name: vm.max_map_count
         value: '262144'
         state: present
         reload: yes

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


The following screenshot displays the result of running docker ps after successfully configuring the ELK instance.

![image](https://user-images.githubusercontent.com/78760503/118497425-0f9a2300-b6f3-11eb-9ba9-5f8bd59299d2.png)



Target Machines & Beats
This ELK server is configured to monitor the following machines: 
   Web1 10.0.0.9 
   Web2 10.0.0.10
   Web3 10.0.0.11  
We have installed the following Beats on these machines:
Filebeat
These Beats allow us to collect the following information from each machine:

Filebeat generates and organizes log files to send to Elasticsearch on the ELK Server. The logs contain information about the file system such as which files have changed and when.



Installing Beats

**Before we completed the below processes its important to navigate to http://[your.VM.IP]:5601/app/kibana. (Replaced [your.VM.IP] with the Public IP address of the ELK server VM).

Installing Filebeat on Web VMs

    Open the ELK server homepage.
        Click on Add Log Data
        Choose System Logs
        Click on the correct OS tab (I used DEB for Linux) under Getting Started to view the correct installation instructions

Installing Metricbeat on Web VMs

    Open the ELK server homepage.
        Click Add Metric Data.
        Click Docker Metrics
        Click on the correct OS tab (I used DEB for Linux) under Getting Started to view the correct installation instructions


Using the Playbook
In order to use the playbook, you will need to have an Ansible control node already configured. Assuming you have such a control node provisioned:

Filebeat
SSH into the control node and follow the steps below:

    Copy the filebeat-configuration.yml file filebeat-configuration to your Ansible container.
    Update the filebeat-configuration.yml file to include:

Line #1106 is where you would edit the file by replacing the IP address with the IP address of your ELK machine

output.elasticsearch:
hosts: ["10.1.0.4:9200"]
username: "elastic"
password: "changeme"

Line #1806 is where you would edit the file by replacing the IP address with the IP address of your ELK machine

setup.kibana:
host: "10.1.0.4:5601"

  Create a Filebeat playbook to install and launch Filebeat on the Web VMs with DVWA.
  When creating ansible playbooks its important to make sure the begging of the playbook is accurate as that is what indicates where and how the tasks will be executed. For Filebeat and Metricbeat playbooks, you want to makesure that you are having the beats installed on hosts: websevers which is how the playbook will know to install the beats on the websevers we want them to be on.
        The Filebeat playbook should look like this: filebeat-playbook.yml

  Run the playbook, and navigate to the Filebeat installation page on the ELK server GUI to check that the installation worked as expected.
  The ELK server GUI is accessible by navigating to http://[your.VM.IP]:5601/app/kibana. You replace [your.VM.IP] with the Public IP address of the ELK server that we created. If it is not working then the ELK server was created incorrectly and or configured incorrectly
  To verify this, scroll down to Step 5: Module Status on the ELK server GUI and click Check Data. If you receive the below image, than the ELK stack was successfully receiving logs:

