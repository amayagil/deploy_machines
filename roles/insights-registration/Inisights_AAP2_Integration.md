Automatic Remediation on AAP2 using Insights
========

Automation controller supports integration with Red Hat Insights. Once a host is registered with Insights, it will be continually scanned for vulnerabilities and known configuration conflicts. Each of the found problems may have an associated fix in the form of an Ansible playbook. Insights users can create a maintenance plan to group the fixes and, ultimately, create a playbook to mitigate the problems. Automation controller tracks the maintenance plan playbooks via an Insights project. Authentication to Insights via Basic Auth is backed by a special Insights Credential, which must first be established in automation controller. To ultimately run an Insights Maintenance Plan, you need an Insights project, and an Insights inventory.

Create Insights Credential
--------------

The firs step is to create an Insights credential, so we can log on our Insights account and retrieve information.

![Insight Credential](https://github.com/amayagil/deploy_machines/blob/main/images/insights-cred.png?raw=true)

Create an Insights Project
--------------

![Insight Project](https://github.com/amayagil/deploy_machines/blob/main/images/insights-project.png?raw=true)

Create Insights Inventory
--------------

![Insight Inventory](https://github.com/amayagil/deploy_machines/blob/main/images/insights-inv.png?raw=true)

![Insight Source](https://github.com/amayagil/deploy_machines/blob/main/images/insights-source.png?raw=true)

![Insight Source Variables](https://github.com/amayagil/deploy_machines/blob/main/images/insights-source-vars.png?raw=true)

![Insight Hosts](https://github.com/amayagil/deploy_machines/blob/main/images/insights-hosts.png?raw=true)

Remediate Insights Inventory
--------------

![Insight Remediation Template](https://github.com/amayagil/deploy_machines/blob/main/images/insights-template.png?raw=true)