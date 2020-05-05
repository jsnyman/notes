# Scheduled Jobs

* Looking for misconfigurations:
  1. Any script or binaries in cron jobs which are writable?
  2. Can we write over the cron file itself.
  3. Is cron.d directory writable?

* With privileges running script that are editable for other users.

* Look for anything that is owned by privileged user but writable for you:

```
crontab -l
ls -alh /var/spool/cron
ls -al /etc/ | grep cron
ls -al /etc/cron*
cat /etc/cron*
cat /etc/at.allow
cat /etc/at.deny
cat /etc/cron.allow
cat /etc/cron.deny
cat /etc/crontab
cat /etc/anacrontab
cat /var/spool/cron/crontabs/root
```

* To make a cron job run now
```
run-parts /etc/cron.daily
```
