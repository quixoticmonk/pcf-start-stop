# pcf-start-stop

This project helps with the automated start and stop of PCF.

Customization for your environment is required.

## Step 1 - Customize Scripts to Your Environment

Create a non-interactive BOSH login [login](https://discuss.pivotal.io/hc/en-us/articles/221695507-How-to-log-on-BOSH-in-non-interactive-scripts-Internal-Article-).

[BOSH docs](https://bosh.io/docs/director-users-uaa.html#client-login).

## Step 2 - Confirm What Deployments Are Running

3. [`ssh` to the Ops Mgr VM](http://docs.pivotal.io/pivotalcf/1-9/customizing/trouble-advanced.html)
3. Find deployment name

`$ bosh deployments`
```
+-------------------------+---------------------------------+-----------------------------------------------+--------------+
| Name                    | Release(s)                      | Stemcell(s)                                   | Cloud Config |
+-------------------------+---------------------------------+-----------------------------------------------+--------------+
| cf-05e71b49a4a7a03a4b91 | binary-buildpack/1.0.5          | bosh-google-kvm-ubuntu-trusty-go_agent/3312.9 | latest       |
|                         | capi/1.11.8                     |                                               |              |

```
## Step 3 - Customize Start/Stop Scripts

### Modify the Start Script

Edit [start-PCF.sh](start-PCF.sh):
1. Update the BOSH client credentials (`BOSH_CLIENT` & `BOSH_CLIENT_SECRET`)
1. Update the BOSH target uri
1. Update the BOSH deployent (make sure to use the deployment file name from `Step 2`)

### Modify the Stop Script

Edit [stop-PCF.sh](stop-PCF.sh):
2. Update the BOSH client credentials (`BOSH_CLIENT` & `BOSH_CLIENT_SECRET`)
2. Update the BOSH target uri
2. Update the BOSH deployent (make sure to use the deployment file name from `Step 2`)

## Step 4 - Upload Scripts to Ops Mgr VM

Use `scp`.


## Step 5 - Schedule Starting and Stoping of PCF

### Sample `crontab` setup

***Don't forget to set the PATH***
```
# Edit this file to introduce tasks to be run by cron.
#
# Each task to run has to be defined through a single line
# indicating with different fields when the task will be run
# and what command to run for the task
#
# To define the time you can provide concrete values for
# minute (m), hour (h), day of month (dom), month (mon),
# and day of week (dow) or use '*' in these fields (for 'any').#
# Notice that tasks will be started based on the cron's system
# daemon's notion of time and timezones.
#
# Output of the crontab jobs (including errors) is sent through
# email to the user the crontab file belongs to (unless redirected).
#
# For example, you can run a backup of all your user accounts
# at 5 a.m every week with:
# 0 5 * * 1 tar -zcf /var/backups/home.tgz /home/
#
# For more information see the manual pages of crontab(5) and cron(8)
#
# m h  dom mon dow   command

PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games



#every business day at 7AM CT start
0       13      *       *      1-5       /home/ubuntu/start-PCF.sh >> /home/ubuntu/pcf-start-stop.log 2>&1

#every business day at 7PM CT stop
0       1      *       *       1-6       /home/ubuntu/stop-PCF.sh >> /home/ubuntu/pcf-start-stop.log 2>&1
```
