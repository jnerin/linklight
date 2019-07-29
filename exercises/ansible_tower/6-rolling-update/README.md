# Exercise 6 - Rolling Updates

In the previous exercise we configured a basic load balanced web service. We may want to perform maintenance at some point such as server patching. In this instance we want to ensure that our web service remains online while we patch. We can use Ansible to perform this orchestration and roll updates through environment while keeping our service online. In this exercise we will perform rolling patch updates and reboots of our servers while keeping our web service online. These are the high-level steps we will take in this exercise - one node at a time.

* Update kernel package (only patching the kernel in this example to save time)
* Remove node from loadbalancer (only if the kernel was patched)
* Remove node from loadbalancer (only if the kernel was patched)
* Reboot node and wait for it to come back online (only if the kernel was patched)
* Add node back into loadbalancer 
* Repeat above steps for next node

## Step 1: Add Rolling Update Job Template

We need to create a template for our Rolling Update play. Navigate to `Templates` and click the `+` button to add a new `job template`. Complete the form using the following entries.

NAME |Web Application Rolling Update
DESCRIPTION|Template for the rolling-update play
JOB TYPE|Run
INVENTORY|Ansible Workshop Inventory
PROJECT|Additional Exercises Project
PLAYBOOK|rolling-update/site.yml
MACHINE CREDENTIAL|Ansible Workshop Credential
LIMIT|
OPTIONS|- [x] Enable Privilege Escalation

## Step 2: Check our loadbalancer is working

Before we run the rolling update, let's check our loadbalanced web service is working as expected. Open a web browser and enter the public IP of node1 as the URL (The IP can be found by looking in the inventory file in /home/student1/lightbulb/lessons/lab_inventory/student1-instances.txt or looking at the node1 details in the Tower UI Inventory).

Refresh your browser a few times to confirm that you are being directed to both node2 and node3. Keep this window open. we are going to check our rolling update is working as expected shortly.

## Step 3: Launch the rolling update job

Press the rocketship `launch` icon to run the job template. You should see the playbook executing against only one node. Once the kernel update has been performed, the node will be removed from the loadbalancer and rebooted. At this point you can refresh your session against the loadbalancer address. You should now only see requests being served from a single node. 

Once the node comes back from it's reboot, it will be added back into the loadbalancer. Once this happens feel free to refresh your browser a few times to confirm that you are getting responses from both nodes again. 

The playbook will now continue onto the next node.

## Step 4: Review the playbook

Take a look at the playbook we are executing in this exercise - https://github.com/pharriso/ansible_workshop/blob/master/rolling_update/site.yml

There are a few things worth noting here. First we are using the `serial` option to tell our playbook to run only on one node at a time.

```yml
{% raw %}
---
- name: perform rolling maintenance of web servers
  hosts: web
  become: yes
  serial: 1
{% endraw %}
```

Also note how we use `delegate_to` in this playbook. This allows us to delagate tasks to other nodes. In this instance we are removing servers from a loadbalancer and are delegating that to our HAProxy loadbalancer server.

```yml
{% raw %}
  - name: remove server from loadbalancer pool
    haproxy:
      state: disabled
      socket: /var/lib/haproxy/stats
      host: "{{ ansible_host }}"
      backend: http
    delegate_to: "{{ groups.loadbalancer | first }}"
    when: kernel_patch is changed
{% endraw %}
```


## Mark Exercise As Completed

Please now run this command:

```bash
cd ~/linklight/exercises/ansible_tower/6-rolling-update/ && ./completed.sh
```



---

[Click Here to return to the Ansible Lightbulb - Ansible Tower Workshop](../README.md)
