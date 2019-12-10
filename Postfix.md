# Postfix Stuff

### Install
```
root@dayprobe:~# apt-get update
root@dayprobe:~# apt-get upgrade
root@dayprobe:~# apt-get install postfix
```

### Redirect incoming mail to pipe
You need to set */etc/postfix/main.cf* to use *alias_maps*:
```
myhostname = domain.tld
myorigin = /etc/mailname
mydestination = domain.tld
relayhost =
mynetworks = 127.0.0.0/8 192.168.31.0/24 [::1]/128 [fe80::]/64
mailbox_size_limit = 0
recipient_delimiter = +
inet_interfaces = all
inet_protocols = ipv4
virtual_alias_maps = hash:/etc/postfix/virtual
alias_maps = hash:/etc/aliases
```
Then create */etc/postfix/virtual* file to use in alises configuration:
```
foo@domain.tld bar
```
And finally you now create */etc/aliases* file
```
postmaster:    root
bar: |"/tmp/run.sh"
```
Final touches:
```
root@server:~# postmap /etc/postfix/virtual
root@server:~# postalias /etc/aliases
root@server:~# /etc/init.d/postfix start
```
