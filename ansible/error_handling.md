# Error Handling 

## Ignore Errors

if the execuation of a certain task is crusial then use  ` any_errors_fatal: false ` in play so the it will forcefully stop the further execution of plays in playbook.

When you wirte a set of tasks/plays in playbook and if one of the play get not executed on mannage node it will stop executing the raste of tasks on that node.
If the next tasks are not dependent on previous tasks then you can ignore errors by using ` ignore_errors: yes ` in ansible-playbook.

Tasks are
- Install the latest versions of openssh and openssl
- Then check if the docker is not install then install the docker on mannage nodes

```
---
- hosts: all
  become: true

  tasks:
    - name: Install security updates
      ansible.builtin.apt:
        name: "{{ item }}"
        state: latest
      loop:
        - openssl
        - openssh
      ignore_errors: yes 
    - name: Check if docker is installed
      ansible.builtin.command: docker --version
      register: output
      ignore_errors: yes    
    - ansible.builtin.debug:
        var: output
    - name: Install docker
      ansible.builtin.apt:
        name: docker.io
        state: present
      when: output.failed
        
```

## Defining Failure

you can ignore specific errors by using `  failed_when: `  For that puropose of getting the output into variable ` copy_result `

```

---
- hosts: all
  become: true

  tasks:
    - name: index.html copy with ignore_error
      template: src=index.html dest=/home/ubuntu
      register: copy_result
      ignore_errors: true

    - name: String Variable from - main_playbook_variable to show into console
      debug:
        var: copy_result


     # 2. any_errors_fatal
    - name: index.html copy with failed_when
      template: src=index.html dest=/home/ubuntu
      failed_when:
        - '"Could not find or access" not in copy_result.msg'
        - copy_result.failed == true
      any_errors_fatal: false

    - name: Create a file if it does not exist
      command: touch /home/ubuntu/myfile.txt
      register: file_created
      changed_when: file_created.rc == 0

```




