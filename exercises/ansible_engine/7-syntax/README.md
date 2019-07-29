# Exercise 7 - Validating playbooks 

There are a number of tools available to verify and validate playbooks prior to running them. In this exercise we will look at ansible syntax checks and best practice checks.


## Step 1 - Create a bad playbook

Let's create a directory for our playbook.

```bash
mkdir ~/validate
cd ~/validate
```

Copy and paste the following contents into `bad_play.yml`

```yml
{% raw %}
---

- hosts: web
  vars:
    my_var: testing
  tasks:
    - name: Install apache
         shell: yum install httpd -y

    - service:
        name: httpd
        state: started
        enabled: true

    - name: Print value of my_var
      debug:
        var: "{{my_var}}"
{% endraw %}
```
## Step 2 - Check playbook syntax

Let's validate the syntax in of our playbook.

```bash
ansible-playbook --syntax-check bad_play.yml
```

The output will display any errors in our playbook. Edit the file and correct the indentation so the shell task lines up as follows.

```yml
{% raw %}
- hosts: web
  tasks:
    - name: Install apache
      shell: yum install httpd -y
{% endraw %}
```
Now re-run the syntax check to confirm you have resolved the syntax issue.

```bash
ansible-playbook --syntax-check bad_play.yml

playbook: bad_play.yml
```

## Step 3 - Check best practices

Our playbook has intentionally been written in a way that goes against a number of best practices. Let's check it. We've already installed ansible-lint, and can use that.

```bash
ansible-lint bad_play.yml
```

You will see a number of issues highlighted with our playbook. Feel free to try to resolve the issues.

This exercise gives you some examples of the tools available to check the YAML syntax and best practices used within your playbooks prior to execution.


## Mark Exercise As Completed

Please now run this command:

```bash
cd ~/linklight/exercises/ansible_engine/7-syntax/ && ./completed.sh
```

---

[Click Here to return to the Ansible Linklight - Ansible Engine Workshop](../README.md)
