# Backup And Restore OpenDS LDAP

### Backup
Backup topic has missing information. I'm not sure whether I'll add it or not.
```
root@server:/tmp/backup/config# ls
./     backup.info
../    config-backup-20070827153501Z

root@server:/tmp/backup/config# cat backup.info
backend_dn=ds-cfg-backend-id=config,cn=Backends,cn=config

backup_id=20070827153501Z
backup_date=20070827153511Z
incremental=false
compressed=true
encrypted=false
property.archive_file=config-backup-20070827153501Z

```


### Restore
```
root@server:~/OpenDS-2.2.1# ./bin/restore --listBackups --backupDirectory ../ldap-backups/2012-03-29/userRoot/
Backup ID:          20120329082317Z
Backup Date:        29/Mar/2012:11:23:18 +0300
Is Incremental:     false
Is Compressed:      false
Is Encrypted:       false
Has Unsigned Hash:  false
Has Signed Hash:    false
Dependent Upon:     none
  
root@server:~/OpenDS-2.2.1# ./bin/restore --backupDirectory backup/userRoot
[04/Sep/2007:08:56:42 -0500] category=JEB severity=INFORMATION msgID=8388693 msg=Restored: 00000000.jdb (size 866409)
root@tselina:~/OpenDS-2.2.1# ./bin/restore --backupDirectory backup/config
root@tselina:~/OpenDS-2.2.1# ./bin/restore --backupDirectory backup/schema
root@tselina:~/OpenDS-2.2.1# ./bin/restore --backupDirectory backup/replicationChanges
root@tselina:~/OpenDS-2.2.1# ./bin/restore --backupDirectory backup/tasks
```