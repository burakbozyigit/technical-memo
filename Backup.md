# Backupninja & Duplicity
```
root@server:~# apt-get install backupninja duplicity python-boto
```

#### /etc/backupninja.conf
*General configuration*
```
# how verbose to make the logs
# 5 -- Debugging messages     (and below)
# 4 -- Informational messages (and below)
# 3 -- Warnings               (and below) 
# 2 -- Errors                 (and below)
# 1 -- Fatal errors           (only)
loglevel = 4
reportemail = automatic_mailer@domain.com
reportsuccess = yes
reportinfo = yes
reportwarning = yes
reportspace = yes
reporthost =
reportuser = ninja
reportdirectory = /var/lib/backupninja/reports
admingroup = root
logfile = /var/log/backupninja.log
configdirectory = /etc/backup.d
libdirectory = /usr/lib/backupninja
usecolors = yes
when = everyday at 01:00
vservers = no
```

#### /etc/backup.d/50-backup.dup
*Backup policy*

```
options = --s3-european-buckets --s3-use-new-style --verbosity 8
testconnect = no
 
[gpg]
password = {GPG_PASSWORD}
 
[source]
exclude = /etc/backupninja.conf
exclude = /etc/backup.d/50-backup.dup
include = /usr/local/bin
include = /etc

[dest]
incremental = no
increments = 9
keep = 60

desturl = s3+http://{BUCKET NAME}/{HOSTNAME}/$(date +%Y-%m-%d)

awsaccesskeyid = {AWS Access ID}
awssecretaccesskey = {AWS Secret Key}
```