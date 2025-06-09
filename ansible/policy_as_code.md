# Policy as Code 

Policy as Code (PaC) in DevSecOps refers to the practice of defining and managing security policies through code. This approach enables automated, consistent, and scalable enforcement of security controls and compliance requirements across the software development lifecycle.


#### Example to enable versioning of all the s3 buckets on aws
- `aws configure` use the access_key to connect the control node with aws
- create playbook
```
---
- name: Enforce s3 bucket versioning on AWS
  hosts: localhost
  gather_facts: false
  tasks:
    - name: list all s3 buckets in aws account
      amazon.aws.s3_bucket_info:
      register: output
    - debug:
        var: output

    - name: enable versioing on s3 bucket
      amazon.aws.s3_bucket:
        name: "{{item.name}}"
        versioning: true
      loop: "{{output.buckets}}"

~
~

```
