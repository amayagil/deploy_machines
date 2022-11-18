Role Name
=========

A role to install Ansible Automation Controller standalone with internal database.

Requirements
------------

AAP2 bundle has to be downloaded or use role-package-downloader to download it.

Role Variables
--------------

| Name | Description | Default |
| ------------ | ------------ | ------------ |
| rhsm_user | The Red Hat Subscription Manager username  | No default, mandatory |
| rhsm_password | The Red Hat Subscription Manager password | No default, mandatory |
| rhsm_pool_id | The pool ID to attach to | No default, mandatory |
| controller_instance_name | DNS of the machine where AC is to be installed |  No default, mandatory |
| controller_version | AC version to be installed |  Defaults to 2.2 |
| checksum_aap2 | AAP2 package checksum, can be retrieved [here](https://access.redhat.com/downloads/content/480/ver=2.2/rhel---9/2.2/x86_64/product-software) |  No default, mandatory |
| pg_database | The name of the postgres database. |  Defaults to awx |
| pg_username | The name of the postgres database username. |  Defaults to awx |
| registry_url | The URL of the image registry. |  Defaults to registry.redhat.io |
| registry_username | The registry username. |  No default, mandatory |
| registry_password | The registry password. |  No default, mandatory |
| host_user | ost user to perform operations depending on the platform it's installed. |  No default |

Dependencies
------------

N/A

Example Playbook
----------------

Here's an example on how to call this role.

```yaml
---
- name: Install AAP2
  hosts: all
  gather_facts: true
  vars:
    controller_instance_name: "controller"
    rh_product_path: "/root"
     
  tasks:
  
    - name: Install an Automation Controller
      ansible.builtin.include_role:
        name: role-ac-install
      when: ansible_os_family == 'RedHat'
```

License
-------

BSD

Author Information
------------------

Amaya Rosa Gil Pippino
