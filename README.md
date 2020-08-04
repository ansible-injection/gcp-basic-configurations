Role Name
=========

Applies basic configurations onto provisioned compute instance(s) on GCP.

- Add a machine-user as sudoer, 
- Generate ssh-key, 
- Install packages
    - openjdk-8-jre
    - openjdk-8-jdk-headless
    - pdsh
- Mount and Format disks 
- Set environment vars

Requirements
------------

Uses standard ssh, so nothing specific to GCP!

Role Variables
--------------

You should provide packages, if you need other fundamentals. You must provide valid user values

defaults/main.yml
```
# GCP connection parameters
general:
    project: sandbox-236618                     #GCP project id
    region: europe-west4
    auth_kind: serviceaccount
    service_account_file: ~/.ssh/ansible.json   #ansible service account file name
scopes:
    - https://www.googleapis.com/auth/compute

# Fundamental Packages to install    
packages: 
    - openjdk-8-jre
    - pdsh

# machine-user details
user:
    xxx:                                  #username: Such as hadoop, elasticsearch etc.. Apply to other fields!
        type: "rsa"                       #ssh-key
        file: "/home/xxx/.ssh/id_rsa"     #username!
        groups: "google-sudoers,adm"      #GCP sudo groups

additional_disks:
    data-disk: 
        path: "/data"
        device: "/dev/sdb"
        fstype: "ext4"
        owner: "xxx"                      #username!
    log-disk: 
        path: "/logs"
        device: "/dev/sdc"
        fstype: "ext4"
        owner: "xxx"                      #username!
```

Dependencies
------------

- gcp_instances: to create instance(s) on GCP

Example Playbook
----------------

[Click](https://github.com/ansible-injection/test-gcp-iaas-roles) to test and see example playbooks.

for configuration.yaml file:

```
- name: Fundamental Compute Instance Configurations
  hosts: 
    - all
  become: yes
  gather_facts: no

  # vars:
    # general:
    #   project: sandbox-236618
    #   region: europe-west4
    #   auth_kind: serviceaccount
    #   service_account_file: ~/.ssh/ansible.json
    # scopes:
    #   - https://www.googleapis.com/auth/compute

    # packages: 
    #     -                                     #Provide if you need other fundamental!! packages
    #     - 
    #     - 

    # user: 
    #     hadoop:                               #username: Provide valid value hadoop/elasticsearch ...
    #         type: "rsa"                       #
    #         file: "/home/hadoop/.ssh/id_rsa"  #Update w/ username
    #         groups: "google-sudoers,adm"

    # additional_disks:
    #     data-disk: 
    #         path: "/data"
    #         device: "/dev/sdb"
    #         fstype: "ext4"
    #         owner: "hadoop"                   #Update w/ username
    #     log-disk: 
    #         path: "/logs"
    #         device: "/dev/sdc"
    #         fstype: "ext4"
    #         owner: "hadoop"                   #Update w/ username


  # tasks:
  
  roles:
    #run w/ default var values
    - role: tansudasli.gcp_basic_configurations 
```

License
-------

Apache-2.0

Author Information
------------------

[tansudasli](github.com/tansudasli)
