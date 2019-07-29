# Exercise 7 - Tower API

Ansible Tower has a RESTFUL API, which we'll explore a little here.

There are 2 versions, v2 being the current one.

## First API Call:

To see the kind of things you can call, try hitting https://public_IP/api/v2/

![apiv2](tower-api-v2.png)

## Calling the API to check status:

Calling the API ping can be a useful 'health' check. https://public_IP/api/v2/ping/

![ping](tower-api-v2-ping.png)


## End Result
We've explored the Tower API briefly.


## Mark Exercise As Completed

Please now run this command:

```bash
cd ~/linklight/exercises/ansible_tower/7-api/ && ./completed.sh
```

---

[Click Here to return to the Ansible Lightbulb - Ansible Tower Workshop](../README.md)
