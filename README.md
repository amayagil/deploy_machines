# UNDER CONSTRUCTION

# Deploy Machines in different cloud providers

This role will help to create and remove instances on several different providers. It can also be used to demonstrate multi cloud use cases.

## How to install this role

This role uses the [Ansible SSA Collection](https://gitlab.com/ansible-ssa/ansible-ssa-collection).

## How to use this role

The role is optimized to be used from the [Playbook RHAAP](https://gitlab.com/ansible-ssa/playbook-rhaap) which will be called by a Job Template created by this role.

If you want to use it in your playbook:

```yaml
- name: deploy instance
  var:
    type: gcp
    instance_name: demo
    instance_flavor: n1-standard-1
  include_role:
    name: ansible.ssa.general.instance
```

To remove or delete a VM or instance, use the same role, but with the "remove" variables set to true.

```yaml
- name: remove instance
  var:
    type: gcp
    instance_name: demo
    remove: true
  include_role:
    name: ansible.ssa.general.instance
```

Instead of using the role directly, consider using the [Ansible SSA Collection](https://gitlab.com/ansible-ssa/ansible-ssa-collection)

## Variables

Check the [defaults/main.yml](./defaults/main.yml) for details on variables used by this role.

# AWS Considerations

## Key pair

Create a key pair with the same name of the VM you are to instanciate prior to anything, or it won't work.

![AWS key pair](https://github.com/amayagil/deploy_machines/blob/main/images/key_pair.png?raw=true)

## Environment variables
### Requirements
Do not forget to set your `collections/requirements.yml` file under your working directory. This file contains the path to the collections so they can be used.

```yaml
$ cat collections/requirements.yml
collections:
  - name: amazon.aws
    version: 3.2.0
    source: https://galaxy.ansible.com/

  - name: community.aws
    version: 3.2.1
    source: https://galaxy.ansible.com/

  - name: ansible_ssa.general
    version: 3.2.88
    source: https://ahub/api/galaxy/content/published/
```

### Create instance
The needed variables to create an instance in AWS using Ansible SSA collection are:
![Destroy AWS instance vars](https://github.com/amayagil/deploy_machines/blob/main/images/create_AWS_vm.png?raw=true)

### Destroy instance
The needed variables to destroy an instance in AWS using Ansible SSA collection are:
![Destroy AWS instance vars](https://github.com/amayagil/deploy_machines/blob/main/images/destroy_AWS_vm.png?raw=true)

In both cases, it is recommended to add it as extra vars in the template in the controller.

### Calling the role with a playbook
This is an example of a playbook to deploy a RHEL machine on an AWS instance:

```yaml
$ cat deploy_aws.yml
---
- name: Deploy EC2 instance
  hosts: all
  gather_facts: false

  tasks:
    - name: Deploy AWS instance
      vars:
        type: ec2
      ansible.builtin.include_role:
        name: ansible_ssa.general.instance
```

## Install an Automation Controller
The very same ```name: ansible_ssa.general.instance``` installs and configures an Automation Controller on a given host, we simply need to set the correct variables and configure the machine credentials for the newly deployed hosts on AWS.

### Credentials
The credentials we now need to add to our template are both the AWS access credentials and the one needed to log in the new instances deployed. Even if we have the key uploaded to EC2, we still need the machine credential to be added to the template in our Automation Controller.

Machine credentials have to be defined in this fashion:
![Machine Credentials AWS](https://github.com/amayagil/deploy_machines/blob/main/images/machine_ec2_creds.png?raw=true)

It is important to note here, that user must be set to ```ec2-user``` and ```Privilege Escalation Method```must be set to ```true```.

Template must be configured on the following fashion:
![Install AC AWS](https://github.com/amayagil/deploy_machines/blob/main/images/install_ac_template.png?raw=true)

It is important to note here that credentials being used are both the AWS credentials and the machine ones. Also, do not forget to set the following variables accordingly for registration into RHSM:
```
rhsm_username
rhsm_password
rhsm_pool_id
```


