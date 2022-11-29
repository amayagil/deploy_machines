Role Name
=========

Remote host configuration client is a tool included with RHC that allows RHEL hosts to connect to the Insights services.

RHC client is not a replacement for insights-client or subscription-manager, it is a an additional utility that complements the user experience around hosted Red Hat Insights services provided by Red Hat Hybrid Cloud Console.

Installing and configuring rhc client on a RHEL host allows you to directly execute Insights remediation playbook directly from the UI context of Red Hat Hybrid Cloud Console for hosts directly registered to Red Hat via Insights.

Requirements
------------

RHC on RHEL 8.5 has dependencies on ansible and rhc-playbook-worker. For installing the dependencies, it will be required to register with subscription-manager in the first place (the role automatically does that based on the RHEL version).

Starting from RHEL 8.6, the registration process requires only one command. However, to get the full benefit of direct remediation via Insights, you will need to install additional packages:
* rhc
* rhc-worker-playbook
* ansible-core

Role Variables
--------------

* redhat_portal_username: (optional)
* redhat_portal_password: (optional)
    
    If defined, these set, change, or remove the username and password in the RHC / Insights configuration.
    If undefined, this role will make no changes to the RHC / Insights configuration.

    If defined to a non-empty string this role will set or change the username and password.
    If defined to an empty string this role will remove the username and password.

    These should be valid username/password for Insights/Red Hat Portal/Red Hat Subscription Manager.

    If the username and password are set in the Insights configuration, they will be used as
    credentials for all future interactions with the Insights server.

    These credentials are only necessary if the client system is not registered with Red Hat
    Subscription Manager.  If the username and password are not set in the Insights configuration,
    which is the default initial state, all interactions with the Insights server will use the
    CERT provided by RHSM.

Dependencies
------------

N/A

Example Playbook
----------------

This is an example of how to use the role:

```yaml
    - name: Prepare machines with Remote Host Configuration
      ansible.builtin.include_role:
        name: role-rhc-install
      when: ansible_os_family == 'RedHat'
```

License
-------

BSD

Author Information
------------------

Amaya Rosa Gil Pippino