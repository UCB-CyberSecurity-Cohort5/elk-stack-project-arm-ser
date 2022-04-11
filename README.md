# ELK-Stack-Project

## Automated ELK Stack Deployment

The files in this repository were used to configure the network depicted below.

![network diagram]()

These files have been tested and used to generate a live ELK deployment on Azure. They can be used to either recreateAnsible/elk_playbook.yml the entire deployment pictured above. Alternatively, select portions of the YML file may be used to install only certain pieces of it, such as elk stack.

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

    - name: elk container
      docker_container:
        name: elk
        image: sebp/elk:761
        state: started
        restart_policy: always
        published_ports:
          - 5601:5601
          - 9200:9200
          - 5044:5044

    - name: docker service enable
      systemd:
        name: docker
        enabled: yes
```
Result of running ansible playbook
```bash
ansible-playbook /etc/ansible/elk_playbook.yml
```  
  
![elk_playbook_output](https://github.com/UCB-CyberSecurity-Cohort5/elk-stack-project-arm-ser/blob/cf6032e47c46c19536369c7cfcc081cd6c2779ac/Diagrams/elk_playbook_output.png)

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
- Filebeat watches for Changes to files on the machine
- Metricbeat collects metrics from the operating system and from services.

The configuration details of each machine may be found below.

| Name      | Function | Private IP Address | Public IP Address | Operating System |
|-----------|----------|--------------------|-------------------|------------------|
| Jump Box  | Gateway  | 10.0.0.4           | 104.45.225.34     | Ubuntu 18.04     |
| ELK Server| Gateway  | 10.1.0.4           | 20.127.3.81       | Ubuntu 18.04     |
| Web-1     | Gateway  | 10.0.0.6           | 13.64.110.152     | Ubuntu 18.04     |
| Web-2     | Gateway  | 10.0.0.7           | 13.64.110.152     | Ubuntu 18.04     |
| Web-3     | Gateway  | 10.0.0.8           | 13.64.110.152     | Ubuntu 18.04     |

### Access Policies

The machines on the internal network are not exposed to the public Internet. 

Only the Jump Box machine can accept connections from the Internet. Access to this machine is only allowed from the following IP addresses:
- `ADMINS IP`

Machines within the network can only be accessed by Jump Box Provisioners.
- ELK VM is accessible from the Jump Box with the IP address:
- `10.1.0.4`

A summary of the access policies in place can be found in the table below.

| Name          | Publicly Accessible | Allowed IP Addresses |
|---------------|---------------------|----------------------|
| Jump Box      | Yes                 | ADMINS IP            |
| ELK Server    | No                  | 10.0.0.4 & ADMINS IP |
| Web-1         | No                  | 10.0.0.4             |
| Web-2         | No                  | 10.0.0.4             |
| Web-3         | No                  | 10.0.0.4             |

### Elk Configuration

Ansible was used to automate configuration of the ELK machine. No configuration was performed manually, which is advantageous because Ansible is...
- Free: open-source
- Simple: no special coding skills needed
- Powerful: can work with very complex models
- Flexible: customizes and orchestrates entire application environment

Ansible hosts File  
![ansible_hosts](https://github.com/UCB-CyberSecurity-Cohort5/elk-stack-project-arm-ser/blob/c29706e8d7cf7f42343a1e71b2f9bd3bdad27697/Diagrams/ansible_hosts.png)

The playbook implements the following tasks:
- Install docker.io
- Install pip3
- Install docker python module
- Increase virtual memory
- Download and launch a docker

The following screenshot displays the result of running `docker ps` after successfully configuring the ELK instance.

![docker ps Output](https://github.com/UCB-CyberSecurity-Cohort5/elk-stack-project-arm-ser/blob/49e067c9ee0a87bb8d1a7a0ca9cc79549bcdc2eb/Diagrams/elk_server_docker_ps.png)

### Target Machines & Beats
This ELK server is configured to monitor the following machines:

| Name      | IP Addresses | Public IP Address |
|-----------|--------------|-------------------|
| Web-1     | 10.0.0.6     | 13.64.110.152     |
| Web-2     | 10.0.0.7     | 13.64.110.152     |
| Web-3     | 10.0.0.8     | 13.64.110.152     |

We have installed the following Beats on these machines:
- Filebeat, Microbeat

These Beats allow us to collect the following information from each machine:
- Filebeat - collects data about the file system
- Metricbeat - collects machine metrics, such as uptime

### Using the Playbook
In order to use the playbook, you will need to have an Ansible control node already configured. Assuming you have such a control node provisioned: 

SSH into the control node and follow the steps below:
- Copy the [playbook](Ansible/elk_playbook.yml) file to Ansible Control Node. 
- Update the [hosts](Ansible/hosts) file to include webservers and elk
- Run the playbook, and navigate to http://[Host IP]/app/kibana#/home to check that the installation worked as expected.
---
- Clone the repo to /etc/ansible

```bash
cd /etc/ansible
mkdir files
git clone https://github.com/UCB-CyberSecurity-Cohort5/elk-stack-project-arm-ser.git
cd elk-stack-project-arm-ser/
mv Ansible/* /etc/ansible/
```
- Copy config files to /etc/ansible/files/

```bash
cp  /etc/ansible/elk-stack-project-arm-ser/*-configuration.yml /etc/ansible/files/
```
- Run all playbooks

```bash
cd /etc/ansible
ansible-playbook elk_playbook.yml
```
```bash
ansible-playbookfilebeat_playbook.yml
```
```bash
ansible-playbookmetricbeat-playbook.yml
```
- Check that the ELK server is running: http://[Host IP]/app/kibana#/home
