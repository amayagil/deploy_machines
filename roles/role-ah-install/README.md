Role Name
=========

A role to install Ansible Private Automation Hub standalone with internal database.

Requirements
------------

AAP2 bundle has to be downloaded or use role-package-downloader to download it. If downloaded, the variable `rh_product_path` must be set.


Role Variables
--------------

| Name | Description | Default |
| ------------ | ------------ | ------------ |
| rhsm_user | The Red Hat Subscription Manager username  | No default, mandatory |
| rhsm_password | The Red Hat Subscription Manager password | No default, mandatory |
| rhsm_pool_id | The pool ID to attach to | No default, mandatory |
| controller_ah_instance_name | DNS of the machine where PAH is to be installed |  No default, mandatory |
| aap_version | AC version to be installed |  Defaults to 2.2 |
| checksum_aap2 | AAP2 package checksum, can be retrieved [here](https://access.redhat.com/downloads/content/480/ver=2.2/rhel---9/2.2/x86_64/product-software) |  No default, mandatory |
| automationhub_admin_password | Automation Hub admin password | Mandatory, defatults to "redhat" |
| automationhub_pg_database | Hostname of the database instance | Mandatory, defatults to "automationhub" |
| automationhub_pg_username | Username to access the database instance | Mandatory, defatults to "automationhub" |
| automationhub_pg_password | Password to access the database instance| Mandatory, defatults to "redhat" |
| automationhub_pg_sslmode | TLS enabled Automation Hub| Mandatory, defatults to "prefer" |
| registry_username | The registry username. |  No default, mandatory |
| registry_password | The registry password. |  No default, mandatory |
| host_user | Host user to perform operations depending on the platform it's installed. |  No default |

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
    controller_ah_instance_name : "ahub"
    rh_product_path: "/root"
     
  tasks:
  
    - name: Install a Private Automation Hub
      ansible.builtin.include_role:
        name: role-ah-install
      when: ansible_os_family == 'RedHat'
```

License
-------

GPL

Author Information
------------------

Amaya Rosa Gil Pippino
