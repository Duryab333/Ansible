# Solutions

## Task 1

- create IAM user with polocy ec2 full access.
 - Security credentials > create access key for that user 
- use valut to secure the access key
```
 openssl rand -base64 2048 > vault.pass
 ansible-vault create group_vars/all/pass.yml --vault-password-file vault.pass
```
Insert the crediantialls of IAM user
- ec2_access_key: --access_key_id
- ec2_secret_key: --secure_key
- create a playbook for creating 3 nodes

```ec2_create.yml :```

```
---
- hosts: localhost
  connection: local

  tasks:
  - name: create ec2 instances 2 of ubuntu and one aws linux 
    amazon.aws.ec2_instance:
      name:  "{{item.name}}"
      instance_type: t2.micro
      key_name: "ansible-node"
      security_group: launch-wizard-11
      region: us-east-1
      aws_access_key: "{{ec2_access_key}}"
      aws_secret_key: "{{ec2_secret_key}}"
      network:
        assign_public_ip: true
      image_id:  "{{item.image_id}}"
      tags:
        Enviornment: "{{item.name}}"

    loop:
      - {image_id: ami-0df8c184d5f6ae949, name: aws-linux }
      - {image_id: ami-04b4f1a9cf54c11d0, name: ubuntu1-linux }
      - {image_id: ami-04b4f1a9cf54c11d0, name: ubunt2-linux }

```
- Run
```
 ansible-playbook ec2_create.yml --vault-password-file vault.pass
```

## Task 2

Do passwordless authenticaion wwith each machine
For that go to the passwordless authenticaion topic

## Task 3

To stop runing machine,use the when conditioning 
- Create invintory file with the user@ip-addesses
- then create yml file
  
``` ec2_stop.yml ```

```
---
- hosts: all
  become: true

  tasks:
    - name: shutdown ubuntu instances only
      ansible.builtin.command:  /sbin/shutdown -t now
      when:
        #ansible_facts['os_family']=='Debian' # for ubuntu machine
        ansible_facts['os_family']=='RedHat'  # for aws liux machine


```
- Run
```
ansible-playbook ec2_stop.yml -i inventory.ini 
```




