# Exercise 10 - Service Now Workflow

Workflows allow you to configure a sequence of disparate job templates (or workflow templates) that may or may not share inventory, playbooks, or permissions. We'll build up a sequence here interacting with ServiceNow and the existing playbooks we've already developed.

## Pre-requisites

Your instructor will have a developer instance up and running for you to use - ask for details.

You need to install the pysnow library on your ansible control node as it's a dependency for the snow_record module

```bash
$ sudo pip install pysnow
```

## Creating Another Project

This has the source for our new playbooks in a separate SCM repo. 

### Step 1:

Select PROJECTS

### Step 2:

Click on ADD ![Add button](at_add.png)

### Step 3:

Complete the form using the following values

NAME|Supplementary Workshop
DESCRIPTION|Additional Workshop Playbooks
ORGANISATION|Default
SCM Type|Git
SCM URL| https://github.com/ffirg/ansible
ISCM UPDATE OPTIONS|- [x] Clean, Delete on Update, Update Revision on Launch

### Step 4:

Click SAVE ![Save button](at_save.png)


## Creating The ServiceNow Record Job Template

We need to create a couple more Job Templates for creating and updating ServiceNow records.

### Step 1:

Select TEMPLATES

### Step 2:

Click on ADD ![Add button](at_add.png), and then select Job Template

### Step 3:
Complete the form using the following values

NAME | Create SNOW Record
DESCRIPTION|Creates a record in ServiceNow
JOB TYPE|Run
INVENTORY|Ansible Workshop Inventory
PROJECT|Supplementary Workshop
PLAYBOOK|tower/snow-create-record.yml
MACHINE CREDENTIAL|Ansible Workshop Credential

Now add the following into EXTRA VARIABLES:
```bash
snow_username: <instructor to provide>
snow_password: <instructor to provide> 
snow_instance: <instructor to provide>
```

### Step 4:
Click SAVE Save button 

## Running The Job Template

Now that you've sucessfully created your Job Template, you are ready to launch it.
Once you do, you will be redirected to a job screen which is refreshing in realtime
showing you the status of the job.


### Step 1:

Select TEMPLATES

---
**NOTE**
Alternatively, if you haven't navigated away from the job templates creation page, you can scroll down to see all existing job templates

---

### Step 2:

Click on the rocketship icon ![Launch button](at_launch_icon.png) for the *Create SNOW record*

### Step 3:

Sit back, watch the magic happen!


### Step 4 (optional):

Try changing the Job Template VERBOSITY to 1 (Verbose) to see the effect on the debug statement in the playbook

### Step 5 (optional):

Go to the ServiceNow Developer Instance, login and check the Incidents. You should see the new ones created by the JT runs.

```bash
https://<dev_instance>.service-now.com
```

## Creating the ServiceNow Update Job Template

Now, let's create the SNOW record update template.

### Step 1:
Select TEMPLATES

### Step 2:

Click on ADD ![Add button](at_add.png), and then select Job Template

### Step 3:
Complete the form using the following values

NAME | Update SNOW Record
DESCRIPTION|Updates a record in ServiceNow
JOB TYPE|Run
INVENTORY|Ansible Workshop Inventory
PROJECT|Supplementary Workshop
PLAYBOOK|tower/snow-update-record.yml
MACHINE CREDENTIAL|Ansible Workshop Credential

Now add the following into EXTRA VARIABLES:
```bash
snow_username: <instructor to provide>
snow_password: <instructor to provide> 
snow_instance: <instructor to provide>
```

### Step 4:
Click SAVE Save button 

NB. I know you want to, but if you try to run the Update SNOW Record Job Template, the job will fail as it's expecting a ServiceNow Record ID which won't be there. When we create the workflow, it'll be passed along from the Create SNOW record job. 


## Create the Workflow Template

Now we've done the ground work and have the playbooks in place, we can connect the sequence up and produce an end-to-end workflow.
This is the exciting bit. Contain yourself for now :)

### Step 1:

Select TEMPLATES 

### Step 2:

Click on ADD ![Add button](at_add.png), and then select Workflow Template

### Step 3:
Complete the form using the following values

NAME | Web Server Request Workflow
DESCRIPTION|Example Workflow
ORGANIZATION|Default
INVENTORY|Ansible Workshop Inventory

### Step 4:
Click the SAVE button 

### Step 5:

Click on the WORKFLOW VISUALIZER button. You'll be taken into the Workflow editor screen.

Click on the Green START box.

A dashed box will appear.

Click on the Create SNOW record template and ensure RUN is set to ALWAYS.

Press the SELECT button.

Now click on the Create SNOW record box and press the green plus button.

For the next dashed box, select the Apache Basic template. Ensure RUN is set to On Success.

Press the SELECT button.

Now click on the Apache Basic box and press the green plus button.

For the next dashed box, select the Update SNOW record template. Ensure RUN is set to On Success.

Press the SELECT button.

### Step 6:
Click the SAVE button 

### Step 7:
Click the SAVE button back on the TEMPLATES page.

## Run the Workflow Template

Click on the rocketship icon ![Launch button](at_launch_icon.png) for the *Web Server Request Workflow*

## Optional: 

The above example showed you how to use extra_vars to inject information into a playbook. This included usernames and passwords, which we may not want to expose normally. 

We can create our own custom credentials and encrypt them in Tower to solve this.

### Step 1: Create a Custom Credential Type

Select Credential Types, and click on ADD ![Add button](at_add.png)

Complete the form using the following values

NAME | ServiceNow

Input configuration (YAML)

```
---
fields:
  - type: string
    id: snow_username
    label: Username
  - secret: true
    type: string
    id: snow_password
    label: Password
  - type: string
    id: snow_instance
    label: ServiceNow Instance
required:
  - snow_username
  - snow_password
  - snow_instance
```

Injector Configuration (YAML)
```yml
{% raw %}
---
extra_vars:
  snow_instance: '{{ snow_instance }}'
  snow_password: '{{ snow_password }}'
  snow_username: '{{ snow_username }}'
{% endraw %}
```

Click the SAVE button 

### Step 2: Create a ServiceNow Credential

Select Credentials, and click on ADD ![Add button](at_add.png)

Complete the form using the following values

Name | SNOW
Credential Type | ServiceNow
Username | studentNN
Password | redhat
ServiceNow Instance | dev68240

Click the SAVE button

### Step 3: Modify the Job Templates

Add the SNOW Credential to the Job Templates and remove the EXTRA VARS information and re-run to test.



## Mark Exercise As Completed

Please now run this command:

```bash
cd ~/linklight/exercises/ansible_tower/10-snow/ && ./completed.sh
```


---

[Click Here to return to the Ansible Lightbulb - Ansible Tower Workshop](../README.md)
