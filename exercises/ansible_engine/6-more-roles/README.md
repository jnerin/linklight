# Exercise 6 - More Roles

For this exercise, you are going to add a further 'common' role to your existing work under exercise 5.

Let's use Ansible Galaxy again to create a role structure. 

## Section 1: Using Ansible Galaxy to initialize a new role

Ansible Galaxy is a free site for finding, downloading, and sharing roles.  It's also pretty handy for creating them which is what we are about to do here.


### Step 1:

Navigate to your `apache-basic-playbook` roles directory.

```bash
cd ~/apache-basic-playbook/roles
```

### Step 2:

Use the `ansible-galaxy` command to initialize a new role called `common/rhel`.

```bash
ansible-galaxy init common/rhel
```

We've created a 'common/rhel' directory so we can always come back and add another directory under 'common' for perhaps other operating systems (really?!) where some differentiation may be required.


## Section 2: Updating Your `site.yml` Playbook With The Newly Created `rhel` Role


### Step 1:

Revise the roles: statements to include and run our common rhel role first based on a conditional - a discovered fact ansible_os_family.

Let's welcome to the party the pre- and post-tasks which might be useful for things we always want to do regardless of what else we run. Here we'll just use logger to announce the start and end of the run. Edit your `site.yml` as follows.

```yml
{% raw %}
---
- hosts: web
  name: This is my updated role-based playbook
  become: yes

  pre_tasks:
    - name: Log what we are doing to {{ messages }}
      shell: /bin/logger 'running {{ role_friendly_text }}'

  roles:
    - { role: common/rhel, when: ansible_os_family == 'RedHat' }
    - apache-simple

  post_tasks:
    - name: Log what we have done to {{ messages }}
      shell: /bin/logger 'finished running {{ role_friendly_text }}'
{% endraw %}
```

### Step 2:

Add some default variables to your role in `roles/common/rhel/defaults/main.yml`.
Let's add a default domain name that we'll assign to each host and the location of the Linux messages file.

```yml
---
# defaults file for common/rhel
messages: /var/log/messages
domainname: ansibleworkshop.local
```

### Step 3:

Add some role-specific variables to your role in `roles/common/rhel/vars/main.yml`.
This is the text we'll spit out to /var/log/messages in the pre- and post-tasks in site.yml

```yml
---
# vars file for common/rhel
role_friendly_text: "COMMON RHEL TASKS like setting hostname, adding users"
```

### Step 4:

Add an motd file in `roles/common/rhel/files/motd`.

```yml

                 _ _     _
  __ _ _ __  ___(_) |__ | | ___
 / _` | '_ \/ __| | '_ \| |/ _ \
| (_| | | | \__ \ | |_) | |  __/
 \__,_|_| |_|___/_|_.__/|_|\___|


```

### Step 5:

Add tasks to your role in `roles/common/rhel/tasks/main.yml`.

```yml
{% raw %}
---
# tasks file for common/rhel

- name: Configure hostname
  hostname:
    name: "{{ inventory_hostname }}.{{ domainname }}"

- name: add user accounts
  user: 
    name: "{{ item.name }}"
    state: present 
    groups: "{{ item.groups }}"
  with_items:
    - {name: 'fred', groups: 'users' }
    - {name: 'wilma', groups: 'wheel' }

- name: Update MOTD
  copy:
    src: files/motd
    dest: /etc/motd
    owner: root
    group: root
    mode: 0444
{% endraw %}
```

## Section 3: Running your new role-based playbook

Now that you've successfully updated your role based playbook,
let's run it and see how it works.

Re-run the playbook.

```bash
ansible-playbook site.yml
```

## Section 4: Checking It Worked

### Step 1:

Let's check it's all worked. We'll SSH into node1 and check the changes.

```bash
grep node1 ~/lab_inventory/hosts
ssh $(grep node1 ~/lab_inventory/hosts | cut -d= -f2) # [use student password]
note: MOTD will be displayed

hostname

sudo grep "COMMON RHEL TASKS" /var/log/messages

grep -E 'fred|wilma' /etc/passwd

grep wheel /etc/group

logout
```

### Step 2: Optional: why not check using ansible itself?

Hint: Use ansible with the command module

Perhaps something like:

```bash
ansible node3 -m command -a "cat /etc/motd"
```

## Section 5: Review

You should now have a completed playbook, `site.yml` with a couple of roles.


## Mark Exercise As Completed

Please now run this command:

```bash
cd ~/linklight/exercises/ansible_engine/6-more-roles/ && ./completed.sh
```

---

[Click Here to return to the Ansible Linklight - Ansible Engine Workshop](../README.md)
