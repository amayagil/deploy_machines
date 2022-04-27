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

Create a key pair with the same name of the VM you are to instanciate prior to anything, or it won't work.

![Red Hat Ansible Automation](https://github.com/amayagil/deploy_machines/blob/main/images/key_pair.png?raw=true)
