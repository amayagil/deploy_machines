insights-client
========

Installs, configures, and registers a system to the [Red Hat Insights service](http://access.redhat.com/insights).  This role is intended to work on Red Hat Enterprise Linux, though it will generally work on any yum based system that has access to the insights-client RPM or the redhat-access-insights RPM.

Role Variables / Configuration
--------------

The following variables can be used to perform some initial configuration for the insights-client install. 
These variables can be passed in directly with the playbook invocation or placed in a configuration yaml file.
See the section 'Example Playbook' for information on various ways to use these variables. 

* insights_display_name: (optional)

    Sets or resets the Display Name/System Name within Insights.  Insights needs an easily identifiable
    name for each system.  If no explicit display name is given to a system, Insights uses it's hostname.
    If a system's hostname is not easily identifiable, like "localhost" or "d4098731408", you can give
    it a better name by setting 'insights_display_name'

    If undefined, this will default to 'inventory_hostname' this is the long external FQDN machine name that would be used for ssh login attempts from an external machine in some other roles, hence setting it this way, allows external ssh connections.

    If defined to be a non-empty string, this role will replace any previously set display name
    for the system with the given string.

* redhat_portal_username: (optional)
* redhat_portal_password: (optional)
    
    If defined, these set, change, or remove the username and password in the Insights configuration.
    If undefined, this role will make no changes to the Insights configuration.

    If defined to a non-empty string this role will set or change the username and password.
    If defined to an empty string this role will remove the username and password.

    These should be valid username/password for Insights/Red Hat Portal/Red Hat Subscription Manager.

    If the username and password are set in the Insights configuration, they will be used as
    credentials for all future interactions with the Insights server.

    These credentials are only necessary if the client system is not registered with Red Hat
    Subscription Manager.  If the username and password are not set in the Insights configuration,
    which is the default initial state, all interactions with the Insights server will use the
    CERT provided by RHSM.

* auto_config: (optional)
    
    True/False - attempt to auto-configure the network connection with Satellite or RHSM. Default behavior is True.

* authmethod: (optional)
    
    BASIC/CERT - This parameter is used to set the authentication method for the Portal. Default bahavior is BASIC.
    Note: when 'auto_config' is enabled (set to True), CERT will be used if RHSM or Satellite is detected.

Facts Installed
---------------

This role installs a new fact 'insights' that provides the system's Insights' System Id.  This System
Id can be used to query about the system with the Insights Service API.

Once this role is run against a system, any future playbook run against that same system will have
the system's Insights System Id in the fact 'ansible_local.insights.system_id'.

For example the task:

    - debug: var=ansible_local.insights.system_id

will display the System Id.

Dependencies
------------

N/A

Example Playbook
----------------

In the examples directory is a very basic playbook utilizing this role:

```yaml
---
- name: Prepare machines for use with Red Hat
  hosts: all
  gather_facts: true

  tasks:
      
    - name: Prepare machines with Insights Registration
      ansible.builtin.include_role:
        name: insights-registration
      when: ansible_os_family == 'RedHat'
```

Example Configuration File
----------------

The insights-client install will be configured by using a configuration yaml file to modify various parameters. 
Here's an example, insights-client-.conf.j2, that configures the insights-client to register via basic auth 
using the provided username/password and display_name (it will populate the /etc/insights-client/insights-client.conf file):

```yaml
[insights-client]
username={{ rhsm_username | default(omit, true) }}
password={{ rhsm_password | default(omit, true) }}
auto_config={{ auto_config | default(omit, true) }}
authmethod={{ authmethod | default(omit, true) }}
display_name="{{ display_name | default(omit, true) }}"
```

If you need to run the Insights Client on a system that is not registered to Red Hat Subscription
Manager, as often happens in testing and demoing, set the redhat_portal_username/redhat_portal_password.

Note: Any of the role variables mentioned earlier can be placed in this configuration file

Example Tags File
----------------
This role also sets the tags for the machines, in this case, it simply sets the group tag to a value set by the cloud_provider variable.

Those are set on the tags.yaml.j2 template that will populate the /etc/insights-client/tags.yaml file. Add as many tags as you want there.

```yaml
---
group: "{{ cloud_provider }}"
```

IMPORTANT:

Due to how remediation playbooks are generated on cloud.redhat.com, this role forces the display_name and ansible_name to being the same (both of them will be inventory_hostname) so it can be used to fix remote machines from the controller.

Author Information
------------------

Amaya Rosa Gil Pippino