# Processes

* Check which processes are running
```
ps aux      # | grep root - to see which are running as root
ps -ef      # | grep root - to see which are running as root
top
```
  - a & x used together ensures all processes listed
  - u - Select by effective user ID (EUID) or name
  - `-e*` using standard syntax
  - `a*` using BSD syntax

* List all available commands and aliases for current session
```
$ compgen -c
```

## Programs or services running as root

* The famous EternalBlue and SambaCry exploit, exploited smb service which generally runs as root.

* If you find that **mysql** is running as root and you username and password to log in to the database you can issue the following commands:
```
select sys_exec('whoami');
select sys_eval('whoami');
```

* Could also use a [User Defined Function]{https://infamoussyn.wordpress.com/2014/07/11/gaining-a-root-shell-using-mysql-user-defined-functions-and-setuid-binaries/}

* pspy - unprivileged linux process snooping
  https://github.com/DominicBreuker/pspy/blob/master/README.md

---


## Service only available from inside host

* Check for applications / processes  not available via port to outside.
* Check for exploits and misconfiguration
```sh
# Linux
$ netstat -anp
$ netstat -antp
```
  - *a* - all, both incoming and outgoing
  - *n* - show numerical address instead of trying to determine symbolic host, port or user
  - *p* - show the PID and name of the program to which each socket belongs.
  - *t* - tcp connections only. Udp could be revealing, but unix domain sockets seem to be useless information
  ```sh
  ?Windows?
  c:\> netstat -ano
  ```

---
## Installed Software

* Check for user installed software with know exploits

```
# Common locations for user installed software
/usr/local/
/usr/local/src
/usr/local/bin
/usr/bin/
/sbin/
/opt/
/home
/var/
/usr/src/

# Debian
dpkg -l

# CentOS, OpenSuse, Fedora, RHEL
rpm -qa (CentOS / openSUSE )

# OpenBSD, FreeBSD
pkg_info

```

## Commands

* To list all the commands + aliases available
```
$ compgen -c
```

## Services Misconfigured

```
cat /etc/syslog.conf
cat /etc/chttp.conf
cat /etc/lighttpd.conf
cat /etc/cups/cupsd.conf
cat /etc/inetd.conf
cat /etc/apache2/apache2.conf
cat /etc/my.conf
cat /etc/httpd/conf/httpd.conf
cat /opt/lampp/etc/httpd.conf
ls -aRl /etc/ | awk '$1 ~ /^.*r.*/
```

## Dynamically Linked Shared Object Library

* How Does the OS Find a Shared Library?
  1. Any directories specified by rpath-link options (directories specified by rpath-link options are only effective at link time)
  2. Any directories specified by –rpath options (directories specified by rpath options are included in the executable and used at runtime)
  3. LD_RUN_PATH
  4. LD_LIBRARY_PATH
  5. Directories in the DT_RUNPATH or DT_RPATH. (DT_RPATH entries are ignored if DT_RUNPATH entries exist
  6. /lib and /usr/lib
  7. Directories within /etc/ld.so.conf


* If an attacker can replace a shared library with a malicious one, then - when the application runs - it will load the malicious code and run it with the executing owner’s permissions. If an application is run as the Root super user, this would result in a full compromise of the host.

Good luck - https://www.contextis.com/en/blog/linux-privilege-escalation-via-dynamically-linked-shared-object-library
