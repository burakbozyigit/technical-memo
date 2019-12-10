### This information IS old.
GitLab installation page has written eons ago. 
I suggest you refer the [Original documentation](https://about.gitlab.com/install/).


# Installation
```
root@eyesat:~# curl https://packages.gitlab.com/install/repositories/gitlab/gitlab-ee/script.deb.sh | sudo bash
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0Detected operating system as Ubuntu/trusty.
Checking for curl...
Detected curl...
100  4206    0  4206    0     0   3735      0 --:--:--  0:00:01 --:--:--  3738
done.
Installing apt-transport-https... done.
Installing /etc/apt/sources.list.d/gitlab_gitlab-ee.list...done.
Importing packagecloud gpg key... done.
Running apt-get update...
Installing apt-transport-https... done.
Installing /etc/apt/sources.list.d/gitlab_gitlab-ee.list...done.
Importing packagecloud gpg key... done.
Running apt-get update... done.
 
root@eyesat:~# apt-get install gitlab-ee
Reading package lists... Done
Building dependency tree      
Reading state information... Done
The following NEW packages will be installed:
  gitlab-ee
0 upgraded, 1 newly installed, 0 to remove and 12 not upgraded.
Need to get 395 MB of archives.
After this operation, 1,094 MB of additional disk space will be used.
0% [Working]
Fetched 395 MB in 1min 27s (4,534 kB/s)
Selecting previously unselected package gitlab-ee.
(Reading database ... 82196 files and directories currently installed.)
Preparing to unpack .../gitlab-ee_8.2.3-ee.0_amd64.deb ...
Unpacking gitlab-ee (8.2.3-ee.0) ...
 
Setting up gitlab-ee (8.2.3-ee.0) ...
gitlab: Thank you for installing GitLab!
gitlab: To configure and start GitLab, RUN THE FOLLOWING COMMAND:
 
sudo gitlab-ctl reconfigure
 
gitlab: GitLab should be reachable at http://eyesat
gitlab: Otherwise configure GitLab for your system by editing /etc/gitlab/gitlab.rb file
gitlab: And running reconfigure again.
gitlab:
gitlab: For a comprehensive list of configuration options please see the Omnibus GitLab readme
gitlab: https://gitlab.com/gitlab-org/omnibus-gitlab/blob/master/README.md
gitlab:
It looks like GitLab has not been configured yet; skipping the upgrade script.
 
root@eyesat:~# gitlab-ctl reconfigure
....
 
Running handlers:
Running handlers complete
Chef Client finished, 207/278 resources updated in 01 minutes 57 seconds
gitlab Reconfigured!
root@eyesat:~#
```


**/etc/gitlab/gitlab.rb**
```
gitlab_rails['ldap_enabled'] = true
gitlab_rails['ldap_servers'] = YAML.load <<-'EOS' # remember to close this block with 'EOS' below
  main: # 'main' is the GitLab 'provider ID' of this LDAP server
    label: 'LDAP 1'
    host: '172.16.31.83'
    port: 389
    uid: 'uid'
    method: 'plain' # "tls" or "ssl" or "plain"
    bind_dn: 'uid=system,ou=system,o=company'
    password: '_PASSWORD_'
    active_directory: false
    allow_username_or_email_login: true
    block_auto_created_users: false
    base: 'ou=users,o=company'
    user_filter: '(|(employeeType=USY)(employeeType=USG))'
    ## EE only
    group_base: 'ou=groups,o=company'
    admin_group: 'cn=svn_all,ou=groups,o=company'
    sync_ssh_keys: false
  secondary: # 'secondary' is the GitLab 'provider ID' of second LDAP server
    label: 'LDAP 2'
    host: '172.16.31.103'
    port: 389
    uid: 'uid'
    method: 'plain' # "tls" or "ssl" or "plain"
    bind_dn: 'uid=system,ou=system,o=company'
    password: '_PASSWORD_'
    active_directory: false
    allow_username_or_email_login: true
    block_auto_created_users: false
    base: 'ou=users,o=company'
    user_filter: '(|(employeeType=USY)(employeeType=USG))'
    ## EE only
    group_base: ''
    admin_group: ''
    sync_ssh_keys: false
EOS
```

# Adding SSL
### SSL Path
```
bbozyigit@server:~$ sudo ls -al /etc/gitlab/ssl/
[sudo] password for bbozyigit:
total 16
drwx------ 2 root root 4096 Feb 29 16:55 .
drwxrwxr-x 3 root root 4096 Apr 28 15:23 ..
-rw-r--r-- 1 root root 3468 Feb 29 16:55 fullchain.pem
-rw-r--r-- 1 root root 1704 Feb 29 16:55 privkey.pem
```

### Configuration
**/etc/gitlab/gitlab.rb**
```
nginx['enable'] = true
nginx['redirect_http_to_https'] = true
nginx['ssl_certificate'] = "/etc/gitlab/ssl/fullchain.pem"
nginx['ssl_certificate_key'] = "/etc/gitlab/ssl/privkey.pem"
```
### Activate Config
```
bbozyigit@server:~$ sudo gitlab-ctl reconfigure
```

# Make LDAP User root
```
root@faithseven:~# gitlab-rails console production
Loading production environment (Rails 4.2.4)
irb(main):001:0>  user = User.where(username: "bbozyigit").first
...
...
name: "Burak Bozyiğit", admin: false
...
...
...
irb(main):003:0> user.admin = true
=> true
irb(main):004:0> user.save!
=> true
irb(main):005:0>  user = User.where(username: "bbozyigit").first
...
...
name: "Burak Bozyiğit", admin: true
...
...
...
```

# Backup GitLab EE
```
root@server:~# mkdir -p /userdata/gitBackups
root@server:~# chown -R git /userdata/gitBackups/
root@server:~# vim /etc/gitlab/gitlab.rb
```

**/etc/gitlab/gitlab.rb**
```
gitlab_rails['manage_backup_path'] = true
gitlab_rails['backup_path'] = "/userdata/gitBackups"
gitlab_rails['backup_archive_permissions'] = 0644 # See: http://doc.gitlab.com/ce/raketasks/backup_restore.html#backup-archive-permissions
gitlab_rails['backup_pg_schema'] = 'public'
gitlab_rails['backup_keep_time'] = 604800
gitlab_rails['backup_upload_connection'] = {
  'provider' => 'AWS',
  'region' => 'eu-west-1',
  'aws_access_key_id' => '{KEY_ID}',
  'aws_secret_access_key' => '{ACCESS_KEY}'
}
gitlab_rails['backup_upload_remote_directory'] = 'git-backups-prod'
gitlab_rails['backup_multipart_chunk_size'] = 104857600
gitlab_rails['backup_encryption'] = 'AES256' # Turns on AWS Server-Side Encryption with Amazon S3-Managed Keys for backups
```

```
root@server:~# gitlab-ctl reconfigure
root@server:~# crontab -l
0 2 * * * /opt/gitlab/bin/gitlab-rake gitlab:backup:create
```