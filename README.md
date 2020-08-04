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

Uses standard ssh, so nothing specific to GCP modules!

- _ansible_ client
- _ansible.cfg_ file
- _hosts_ file
- gcp account

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

hosts
```
# static inventory file

# group w/ alias suh as hadoop/bigdata/ ....
# Inspire from network Tags in GCP while host grouping
# Update IPs
# possible params: ansible_host=, ansible_user=, ansible_ssh_pass=, ansible_connection=ssh/winrm/localhost
[web]
standalone-node ansible_host=34.90.161.000
```

ansible.cfg
```
[defaults]
host_key_checking = False
inventory = hosts

remote_user = tansudasli                          #your gcp account
private_key_file = ~/.ssh/google_compute_engine   #If set, always uses this for authentication

[inventory]
# List of enabled inventory plugins and the order in which they are used.
enable_plugins = host_list, script, yaml, ini, auto, gcp_compute

```

configuration.yaml
```
- name: Fundamental Compute Instance Configurations
  hosts: 
    - all
  become: yes
  gather_facts: no

  # vars:

    # Installs openjdk-8-jre and pdsh at default
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
