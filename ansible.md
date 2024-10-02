### *Build 3 instances t2.micro on amazon linux, controller, host-manager-1 and host-manager-2*
- hostnamectl set-hostname controller
- bash
- ssh-keygen
- ip a s
- cd .ssh
- vim authorized_keys
- cat id_rsa.pub
  
#### *Here incase of controller copy paste the public key of other two instances and paste the keys in the vim file and likewise do the same for other 2 instances to establish connection*

### *Controller*
- systemctl restart sshd 
- systemctl enable sshd

#### *Use ping command to check if the connection is established between the instances and allow ICMP port in network security*

### *Controller -   Install ansible*

- yum install ansible* -y
- ansible --version
- cd /etc/ansible/
- vim ansible.cfg

### *Paste the below content of ansible.cfg inside the vim file*

- ansible --version

  #### *Create vim host under etc/ansible*
- vim hosts
  *paste ip of the two hosts in format*
  
   [us-server]
  
   172.31.1.23
  
- ansible all --list-hosts
- ansible all -m ping
  *type yes 2 times*
  
#### *When the output is green command is executed, when gold that means ansible did changes in the remote machine and when in red it indicates error*

### *Configure web server*
 - cd /etc/ansible
 - vim configure.yml

#### *Paste the below content inside vim file*

```yaml

   ---

- name: configure apache server
  hosts: all

  tasks:

     - name: installed httpd pkg
       dnf:
            name: httpd
            state: latest
     - name: copy index.html file
       copy:
            src: index.html
            dest: /var/www/html/index.html
     - name: started apache
       systemd:
             name: httpd
             state: started
             enabled: true

```

       
 - ansible-playbook configure.yml --syntax-check
 - cat >index.html
 - ansible-playbook configure.yml  
   #### *paste the public ip of the host*

   ### *To make a group and a user using script*

    vim playbook.yml

#### *Paste the below content inside the vim file*

```yaml
---

- name: creating some user & group
  hosts: all


  tasks:

    - name: create a group
      group:
           name: devops
           state: present
    - name: create an user thor
      user:
           name: thor
           uid: 1200
           shell: /bin/bash
           home: /home/india
           groups: devops
           state: present
    - name: install smb pkg
      yum:
           name: cifs-utils
           state: present
    - name: install ftp
      yum:
           name: nfs-utils
           state: present
```

- ansible-playbook playbook.yml
 
#### *Go to host and run the below command to check the user created inside group*

- cat /etc/group

### *Handler*
- vim configure-appache.yml   (*Inside ansible directory*)
(*Paste the below content*)

```yaml
  ---
- name: configure apache with handler
  hosts: all
  tasks:
    - name: installed httpd
      dnf:
        name: httpd
        state: latest
 
    - name: copied httpd.conf file on target machine
      copy:
        src: httpd.conf
        dest: /etc/httpd/conf/httpd.conf
 
    - name: copied index.html
      copy:
        src: index.html
        dest: /var/www/html/index.html
 
    - name: restart the httpd service
      systemd:
        name: httpd
        state: restarted
        enabled: true
      notify: restart_httpd
  
  handlers:
    - name: restart_httpd
      service:
        name: httpd
        state: restarted
        enabled: true
 
    - name: restart_firewalld
      service:
        name: firewalld
        state: restarted
        enabled: true
```

(*Now make an index file*)
  - cat > index.html
  - yum install httpd -y
  - cd /etc/ansible
  - vim /etc/httpd/conf/httpd.conf     (#*Add a comment at start #Mahek Shetty and then save it*)
  - ll              (#*Check for index.html file*)
  - ansible-playbook configure-appache.yml --syntax-check     (#*Handler and task should have same indentation*)
  - ansible-playbook configure-appache.yml

 #### (*Now go to host and check if the files are reflecting*)

(*To check if httpd is installed*)
 -  rpmquery httpd
    
(*To check if the index html file is available*)
 -  cd /var/www/html
 -  ll
   
(*To check conf file*)
 -  cd /etc/httpd/conf
 -  ll
   
(*Check if the changes made in the /etc/httpd/conf/httpd.conf is reflecting*)
 - cat httpd.conf
 - ip a s
 - curl http://172.31.34.36

### *Configure AWS with ansible*

-  cd /etc/ansible
-  vim creds.yml        (#*Paste the aws_access_key: and aws_secret_key: *)
-  vim ec2.yml

#### *Here change the ami, region, security group, key name*

```yaml
  ---
- name: create an ec2 instance
  hosts: all
  vars_files:
        - creds.yml
  tasks:
    - name: install pip
      yum:
        name: pip
        state: present
    - name: install boto3
      pip:
        name: boto3
        state: present

    - name: create an ec2 instance using ansible
      amazon.aws.ec2_instance:
        aws_access_key: "{{ aws_access_key }}"
        aws_secret_key: "{{ aws_secret_key }}"
        key_name: "ans-key"
        instance_type: t2.micro
        security_group: sg-0891eca823714b7e5
        region: ap-southeast-2
        count: 1
        image_id: ami-0e8fd5cc56e4d158c
        tags:

```

 ansible-playbook ec2.yml




   
