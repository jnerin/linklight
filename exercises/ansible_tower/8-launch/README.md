# Exercise 8 - Tower API Job Launch (incl. Job Slicing)

We can call into Ansible Tower via the API to run a job template for us.

We'll use the apache-basic playbook example we did earlier to demonstate this.

### Step 1: Find the Job ID

We need to find the job ID so we can launch it via the api. In the Tower UI got to `Templates` and then click on `Apache Basic Job Template`. In the url you will see the job ID. For example - https://X.X.X.X/#/templates/job_template/8 - This shows my job ID is `8`.


### Step 2: Launch the job

We'll use the *curl* command to launch the job. It's a bit of a handful but let's break it down, so it's easier to understand:

```bash
-- user:    who we authenticate as. In our case the admin account
-k:         insecure HTTPS. So we don't check for valid certs
-s:         silent mode. Cuts out some of the not so -useful curl output we don't want
-H:         HTTP JSON MIME type headers. We need to POST in the extra_vars and job_tags so the job will run successfully
```

NB. You will need to check and change where necessary the PUBLIC_IP for your Tower instance and the Job Template number (mine here is 8)

Lastly, we use a little bit of python magic to prettify the output, making it more readable.

**NOTE**
Make sure you update the password below from PASSWORD to your Tower admin password. You also need to update the IP address in the URL to be the public IP address of your Tower server. Finally, you also need to update your job template ID. In the below example we are using job template ID 8 - https://X.X.X.X/api/v2/job_templates/`8`/launch/

---


```bash
curl --user 'admin':'PASSWORD' -k -s -H 'Content-Type: application/json' -d '{"extra_vars":"{\"apache_test_message\":\"Job launch from API\"}"}' -k -s  -XPOST https://X.X.X.X/api/v2/job_templates/8/launch/ | python -m json.tool
```

You should see some output from the job launch including the ID of this particular job run.

```bash
    },
    "timeout": 0,
    "type": "job",
    "unified_job_template": 8,
    "url": "/api/v2/jobs/`58`/",
    "use_fact_cache": false,
    "vault_credential": null,
    "verbosity": 0
}
```

## Checking The Job

From the above output, I can see `job 58` so that's what we should look for in Tower under Jobs.

## End Result
We've explored the Tower API *Job Launch* facility in order to run a job template.



## Mark Exercise As Completed

Please now run this command:

```bash
cd ~/linklight/exercises/ansible_tower/8-launch/ && ./completed.sh
```


---

[Click Here to return to the Ansible Lightbulb - Ansible Tower Workshop](../README.md)
