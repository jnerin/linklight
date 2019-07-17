# Exercise 5 - Tower workflows

We are going to change our deployment. We will deploy a loadbalancer sitting in front of two of our webservers. To do this we will re-purpose node1 as our loadbalancer. We will also include a check playbook which will confirm if both of our web servers are responding. We are also going to use workflows in this exercise to tie all of our playbooks together into a single automation job. Workflows allow you to configure a sequence of disparate job templates (or workflow templates) that may or may not share inventory, playbooks, or permissions. 

### Step 1: Update our inventory

We need to amend our inventory so that node1 is part of a loadbalancer group.

```bash
cd 
git clone https://github.com/pharriso/ansible_workshop.git
cd ~/ansible_workshop/build-new-inventory
ansible-playbook -i ~/lightbulb/lessons/lab_inventory/student##-instances.txt generate_inventory.yml
```
Take a look at our new inventory file. node1 should now be in the loadbalancer group.

```bash
cat /tmp/inventory
```
Let's import the new inventory.

```bash
sudo tower-manage inventory_import --source=/tmp/inventory --inventory-name="Ansible Workshop Inventory" --overwrite --overwrite-vars
```

Log into the Towerr UI, go to `Inventories`, `Ansible Workshop Inventory` and then press the `hosts` button. Check that node1 is now in the loadbalancer group only and not in the web group anymore. If node1 is not removed from the web group, either re-run the previous command or remove it manually fron the web group in the tower inventory.

### Step 2: Create a new project

We need to add a couple of additional playbooks. These are in a different git repository so we need to add a new project. In the Ansible Tower UI, go to `Projects` and then click the `+` button to add a new project. Complete the form using the following entries.

NAME | Additional Exercises Project
------|------------------------------------------------
DESCRIPTION| Additional Exercises playbooks
ORGANIZATION| Default
SCM TYPE| Git
SCM URL| https://github.com/pharriso/ansible_workshop.git
SCM BRANCH|
SCM UPDATE OPTIONS| [x] Clean <br />  [x] Delete on Update<br />  [x] Update on Launch

### Step 3: Create Job Templates

First let's create a template to configure our loadbalancer. Navigate to `Templates` and click the `+` button to add a new `job template`. Complete the form using the following entries.

NAME |Loadbalancer Basic Job Template
DESCRIPTION|Template for the loadbalancer-example play
JOB TYPE|Run
INVENTORY|Ansible Workshop Inventory
PROJECT|Additional Exercises Project
PLAYBOOK|loadbalancer-example/site.yml
MACHINE CREDENTIAL|Ansible Workshop Credential
LIMIT|
OPTIONS|- [x] Enable Privilege Escalation

Add another job template for our loadbalancer checks. Complete the form using the following entries.

NAME |Loadbalancer Check job Template
DESCRIPTION|Template for the loadbalancer-check play
JOB TYPE|Run
INVENTORY|Ansible Workshop Inventory
PROJECT|Additional Exercises Project
PLAYBOOK|loadbalancer-check/site.yml
MACHINE CREDENTIAL|Ansible Workshop Credential
LIMIT|
OPTIONS|- [x] Enable Privilege Escalation

### Step 3: Create a workflow template

Now let's join our three seperate playbooks into a single automation workflow. Navigate to `Templates` and click the `+` button to add a new `workflow template`.

NAME |Web Application Workflow
DESCRIPTION|Workflow to deploy loadbalancer and web servers
ORGANIZATION|Default
INVENTORY|Ansible Workshop Inventory

Save the workflow template. Once saved, click on the `workflow visualizer`. Click on the `start` box to start building the workflow. Select the `Loadbalancer Basic job Template` and then press `select`. 

Now click on `Loadbalancer Basic job Template` and press the green `+` to add the next playbook to our workflow. Now select `Apache Basic Job Template` and press `select` again. You can optionally update the message that will be displayed on your website by clicking the `prompt` button and editing the text.

Finally, click on the `Apache Basic Job Template`, press the green `+` and add the `Loadbalancer Check job Template`. Press `select` and then press `save`.

### Step 4: Launch Job Template

Now let's try launching our workflow. Press the rocketship `launch` icon to run the job template.


### Step 5: Testing our application

The last job in the workflow checks our loadbalancer is working as expected. It also prints the public IP address for the loadbalancer. Click on the job details to get your loadbalancer address and then use your web browser to view the url. Refresh your browser to confirm that you are loadbalancing between node2 and node3.

---

[Click Here to return to the Ansible Lightbulb - Ansible Tower Workshop](../README.md)
