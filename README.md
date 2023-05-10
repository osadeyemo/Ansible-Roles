

Solution

🌟 ANSIBLE ROLES 🌟
🔰 Ansible Architecture :
![image](https://user-images.githubusercontent.com/122319918/224522571-a8eb3093-2a5b-45da-a033-a416917abf81.png)

 
roles-haproxy-httpd
1.	Create an ansible role "myapache" to configure httpd WebServer
2.	Create another ansible role "myloadbalancer" to configure HAProxy LB.
3.	We need to combine both of these roles controlling webserver versions and solving challenge for host ip's addition dynamically over each Managed Node in HAProxy.cfg file.


Roles let you automatically load related vars_files, tasks, handlers, and other Ansible artifacts based on a known file structure. Once you group your content in roles, you can easily reuse them and share them with other users.

🔰 So let’s begin….

 ![image](https://user-images.githubusercontent.com/122319918/224522592-e123704a-f6f2-4d16-a843-edd9f47ca6e2.png)


♦️ Inventory File :


[load_balancer]
172.31.61.217    ansible_user=ec2-user   ansible_ssh_private_key=/root/ANSIBLE.pem   ansible_connection=ssh
[backend_server]
172.31.62.60    ansible_user=ec2-user   ansible_ssh_private_key=/root/ANSIBLE.pem   ansible_connection=ssh
172.31.63.141    ansible_user=ec2-user   ansible_ssh_private_key=/root/ANSIBLE.pem   ansible_connection=ssh
[apache_webserver]
172.31.50.174    ansible_user=ec2-user   ansible_ssh_private_key=/root/ANSIBLE.pem   ansible_connection=ssh
[servers:children]
backend_server
apache_webserver

 Ansible Configuration File :
[defaults]
inventory=/root/ip.txt
host_key_checking=False
ask_pass=False
remote_user=ec2-user
private_key_file=/root/hadoop.pem
roles=/root/

[privilege_escalation]
become=True
become_method=sudo
become_user=root
become_ask_pass=False

🔅. install ansible on ansible-machine
sudo yum update -y
sudo amazon-linux-extras install ansible2 -y

🔅 Create an ansible role “myapache” to configure httpd WebServer.

         ansible-galaxy role init myapache 

🔅 Create another ansible role “myloadbalancer” to configure HAProxy LB.

         ansible-galaxy role init myloadbalancer

♦️ Here, you can see two roles created :

         ansible-galaxy role list --roles-path /root/


Check above repo to put contents into individual role folders and files   


In main playbook setup.yml ,
Setup.yml 

- hosts: servers
  roles:
      - role: /root/myapache

- hosts: load_balancer
  roles: 
      - role: /root/myloadbalancer

Now running the playbook ,

ansible-playbook setup.yml
♦️ Now to check on browser :
http://<loadbalancer_ip>:8080/<web_page>

