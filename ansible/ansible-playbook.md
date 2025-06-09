# Ansible-Playbook

A Playbook is a YAML file that defines a series of actions to be executed on managed nodes. It contains one or more "plays" that map groups of hosts to roles.

### Example 

This ansible playbook contain two tasks. one is installation of apache server and second is copy a html file from control node to mannage node.

```
---
- hosts: all
  become: true
  tasks:
    - name: instal apache httpd
      ansible.builtin.apt:
        name: apache2
        state: present
        update_cache: yes
    - name: Copy file with owner and permissions
      ansible.builtin.copy:
        src: index.html
        dest: /var/www/html
        owner: root
        group: root
        mode: '0644'

```

To run a playbook 

```
ansible-playbook -i inventory.ini install_apache_server.yml
```

## Ansible Colections

### AWS Collecions

Pre-requisit install AWS collections on Ansible-mannage mode

```
 ansible-galaxy collection install amazon.aws
```


#### AWS EC2 Deployment

conside control node as aws-ec2 isntance
- Create aws ec2 instance
-  Install
Ubuntu
-  ```
   sudo apt install ansible
   sudo apt install pipx
   pipx install boto3 --include-deps
   pip instal boto3
   ```
AWS Linux
```
   sudo yum install python-pip
   sudo amazon-linux-extras install ansible2
   pip install boto 
   ```
## 1) Attaching IAM role

Attach AMI role: full Ec2 acress
  
- Creat a playbook.yml file
```
--- 
- hosts: localhost
  connection: local
  tasks:
  - name: start an instance with a public IP address
    amazon.aws.ec2_instance:
      name: "ansible-instance"
      # key_name: "prod-ssh-key"
      # vpc_subnet_id: subnet-013744e41e8088axx
      instance_type: t2.micro
      security_group: default
      region: us-east-1
      aws_access_key: "{{ec2_access_key}}"  # From vault as defined
      aws_secret_key: "{{ec2_secret_key}}"  # From vault as defined      
      network:
        assign_public_ip: true
      image_id: ami-04b70fa74e45c3917
      tags:
        Environment: Testing
```
- Check the syntax of file by
```
  ansible-playbook --syntax-check playbook.yml
```
- Check locally the playbook

```
  ansible-playbook -C file_name.yml
```
- To Run the playbook
```
 ansible-playbook playbook.yml
```
## 2) Use Access key

- Setup Vault: to secure access key of aws

Create a password for vault

```
 openssl rand -base64 2048 > vault.pass
```
- ec2_access_key: --access_key_id
- ec2_secret_key: --secure_key

Add your AWS credentials using the below vault command

```
 ansible-vault create group_vars/all/pass.yml --vault-password-file vault.pass
```
- To run ansible-paybook
```
 ansible-playbook ec2_create.yml --vault-password-file vault.pass
```
