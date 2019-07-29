# Exercise 3a - Creating a Job Schedule

A job can be scheduled to run on a regular basis aka cron style. This exercise will show you how.


## Select a Job Template

### Step 1:

Select TEMPLATES

### Step 2:

Click on the Apache Basic Template

### Step 3:

Click on SCHEDULES

### Step 4:

Click the ADD button ![Add button](at_add.png)

### Step 5:

Add these details under CREATE SCHEDULE

NAME |Regular Apache Run
START DATE|Select todays date
START TIME|Select a time a couple of minutes from now
LOCAL TIME ZONE|Europe/London
REPEAT FREQUENCY|None (run once)

### Step 6:
Click SAVE ![Save button](at_save.png)

## Viewing Schedules

### Step 1:
Click on Schedules

You'll see your job listed and ON (enabled), which will be run shortly.
You can see the job schedule run output as per normal under Jobs

## End Result
You've added a job to run on a schedule.


## Mark Exercise As Completed

Please now run this command:

```bash
cd ~/linklight/exercises/ansible_tower/3a-schedule/ && ./completed.sh
```

---

[Click Here to return to the Ansible Lightbulb - Ansible Tower Workshop](../README.md)
