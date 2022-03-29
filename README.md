# ELK-Stack-Project

## Automated ELK Stack Deployment

The files in this repository were used to configure the network depicted below.

![network diagram](https://github.com/UCB-CyberSecurity-Cohort5/elk-stack-project-arm-ser/blob/ea440d70582a0f39c7d10b269577732ce3115d83/Diagrams/network%20diagram.jpg)

These files have been tested and used to generate a live ELK deployment on Azure. They can be used to either recreate the entire deployment pictured above. Alternatively, select portions of the YML file may be used to install only certain pieces of it, such as Filebeat.

```yml
---
- name: elk config
  hosts: elk
  remote_user: redadmin
  become: true
  tasks:
    - name: docker.io installation
      apt:
        update_cache: yes
        name: docker.io
        state: present

    - name: pip3 installation
      apt:
        name: python3-pip
        state: present
 
    - name: docker module installation
      pip:
        name: docker
        state: present
 
    - name: use more memory
      sysctl:
        name: vm.max_map_count
        value: "262144"
        state: present
        reload: yes

    - name: download and enable docker elk container
      docker_container:
        name: elk
        image: sebp/elk:761
        state: started
        restart_policy: always
        published_ports:
          - 5601:5601
          - 9200:9200
          - 5044:5044

    - name: enable docker service
      systemd:
        name: docker
        enabled: yes
```

This document contains the following details:
- Description of the Topology
- Access Policies
- ELK Configuration
  - Beats in Use
  - Machines Being Monitored
- How to Use the Ansible Build


### Description of the Topology

The main purpose of this network is to expose a load-balanced and monitored instance of DVWA, the D*mn Vulnerable Web Application.

Load balancing ensures that the application will be highly reliable and available, in addition to restricting unauthorized access to the network.
- Load Balancer sits between web server and clients, and distributes the load between multiple pre-configured web servers. Being between clients and servers Load Balancer provides additional security, by maintaining additional layer of firewall and protecting servers from DDOS attacks.  
- A Jump Box is a middle machine between administrators and web servers. Admins connect to it and use it as yet another security layer before connecting to servers to ensure servers proper isolation. 

Integrating an ELK server allows users to easily monitor the vulnerable VMs for changes to the Jump Box and system Network.
- _TODO: What does Filebeat watch for?_
- _TODO: What does Metricbeat record?_

The configuration details of each machine may be found below.
_Note: Use the [Markdown Table Generator](http://www.tablesgenerator.com/markdown_tables) to add/remove values from the table_.

| Name      | Function | IP Address | Operating System |
|-----------|----------|------------|------------------|
| Jump Box  | Gateway  | 10.0.0.4   | Linux            |
| ELK Server| Gateway  | 10.1.0.4   | Linux            |
| Web-1     | Gateway  | 10.0.0.6   | Linux            |
| Web-2     | Gateway  | 10.0.0.7   | Linux            |

### Access Policies

The machines on the internal network are not exposed to the public Internet. 

Only the Jump Box machine can accept connections from the Internet. Access to this machine is only allowed from the following IP addresses:
- `ADMINS IP ADDRESS`

Machines within the network can only be accessed by Jump Box Provisioners.
- ELK VM is accessible from the Jump Box with the IP address:
- `10.1.0.4`

A summary of the access policies in place can be found in the table below.

| Name      | Publicly Accessible | Allowed IP Addresses |
|-----------|---------------------|----------------------|
| Jump Box  | Yes                 | ADMINS IP ADDRESS    |
| ELK Server| No                  | 10.0.0.4             |
| Web-1     | No                  | 10.0.0.4             |
| Web-2     | No                  | 10.0.0.4             |

### Elk Configuration

Ansible was used to automate configuration of the ELK machine. No configuration was performed manually, which is advantageous because...
- _TODO: What is the main advantage of automating configuration with Ansible?_

The playbook implements the following tasks:
- _TODO: In 3-5 bullets, explain the steps of the ELK installation play. E.g., install Docker; download image; etc._
- ...
- ...

The following screenshot displays the result of running `docker ps` after successfully configuring the ELK instance.

![docker ps Output](https://github.com/UCB-CyberSecurity-Cohort5/elk-stack-project-arm-ser/blob/49e067c9ee0a87bb8d1a7a0ca9cc79549bcdc2eb/Diagrams/elk_server_docker_ps.png)

### Target Machines & Beats
This ELK server is configured to monitor the following machines:

| Name      | IP Addresses |
|-----------|--------------|
| Web-1     | 10.0.0.6     |
| Web-2     | 10.0.0.7     |

We have installed the following Beats on these machines:
- Microbeats

These Beats allow us to collect the following information from each machine:
- Filebeat - collects data about the file system
- Metricbeat - collects machine metrics, such as uptime

### Using the Playbook
In order to use the playbook, you will need to have an Ansible control node already configured. Assuming you have such a control node provisioned: 

SSH into the control node and follow the steps below:
- Copy the ![playbook](Ansible/elk_playbook.yml) file to Ansible Control Node. 
- Update the ![hosts](Ansible/hosts) file to include webservers and elk
- Run the playbook, and navigate to http://[Host IP]/app/kibana#/home to check that the installation worked as expected.

_TODO: Answer the following questions to fill in the blanks:_
- _Which file is the playbook? Where do you copy it?_
- _Which file do you update to make Ansible run the playbook on a specific machine? How do I specify which machine to install the ELK server on versus which to install Filebeat on?_
- _Which URL do you navigate to in order to check that the ELK server is running?

_As a **Bonus**, provide the specific commands the user will need to run to download the playbook, update the files, etc._
