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

Force Insights Issue
--------------
In order to show a common security vulnerability on Red Hat Linux systems, we are forcing our machines to have installed the adobe flash plugin. In order to do that we can use the `prepare-insights-issue.yml` playbook, available on this demo. Do this before running the next template.

Once we've run that playbook, we would be able to see all our systems hitting the security vulnerability "Decreased security: Adobe Flash Player installed", as shown in the image below:

![Insight Advisor](https://github.com/amayagil/deploy_machines/blob/main/images/insights-adobeflash.png?raw=true)

Since that has a playbook to remediate, we will generate one in the Insights console for its use with Ansible Automation Platform as follows.

On the Advisor --> Recommendations menu, we select the machines affected by the vulnerabily and click on remediate, which will allow us to create a remediation playbook (we will call this adobe-flash, but feel free to chose your prefered name).

![Insight Remmediation Playbook](https://github.com/amayagil/deploy_machines/blob/main/images/insights-pb.png?raw=true)

IMPORTANT: do not forget to sync your project, so it can pull the latest remediation playbook you have just created.

Remediate Insights Inventory
--------------

![Insight Remediation Template](https://github.com/amayagil/deploy_machines/blob/main/images/insights-template.png?raw=true)