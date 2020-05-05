# Priv Esc for Linux

## Information Gathering

### Enumeration Scripts

TODO: Here I need pick the scripts that i want to run.

* LinEnum

Also see the **Tools** section below for more scripts.

---


## Information Gathering

* Who am I
```
$ whoami
$ id
$ id [user]
```

* Where am i
```
$ pwd
```

* Who are the [other users](users.md#other_users)

* What are my [sudo](sudo.md) rights?
```
$ sudo -l
```

* Check what the [environment variables](linux_command_line.md#environment_variables)
  - Any users with a [bad path configuration]?  


* Check which [processes](processes.md) are running.
  - Which are running as root?
  - Which services are only available from inside the host?
  - What software has been installed?
  - Any services misconfigured?

* What interesting [files and folders](files_and_folders.md#interesting) can be found?

* Which files / folders are [writable](files_and_folders.md#writable)?
  - Check for writable `/home` folders -> [ssh key](files_and_folders.md#ssh_key)

* Which files / folders have the suid and guid permissions set?

* Any files contain [passwords](files_and_folders.md#passwords)?

* Any misconfigurations / exploitable in the [scheduled jobs](scheduled_jobs.md)?

* Any [unmounted filesystems](files_and_folders#filesystems)?

* Any misconfigured [NFS share](files_and_folders#nfs_share)?

* How about [Dynamically Linked Shared Object Libraries]()?

---

## Other information

* Check for [network](networking.md) resources.


---
## Vectors of Privilege Escalation


* https://www.hackingarticles.in/linux-privilege-escalation-via-automated-script/ (from above)
  - OS Detail & Kernel Version
  - Any Vulnerable package installed or running
  - Files and Folders with Full Control or Modify Access
  - File with SUID Permissions
  - Mapped Drives (NFS)
  - Potentially Interesting Files
  - Environment Variable Path
  - Network Information (interfaces, arp, netstat)
  - Running Processes
  - Cronjobs
  - User’s Sudo Right
  - Wildcard Injection

* https://xapax.gitbooks.io/security/content/privilege_escalation_-_linux.html
- Kernel exploits
- Programs running as root
- Installed software
  - Weak/reused/plaintext passwords
  - Inside service
  - Suid misconfiguration
  - Abusing sudo-rights
  - World writable scripts invoked by root
  - Bad path configuration
  - Cronjobs
  - Unmounted filesystems

---
## Privilege Escalation Enumeration Tools

Another source used these three: 1, 6, 7

### Enumeration Tools

1. **LinEnum**  
  Scripted Local Linux Enumeration & Privilege Escalation Checks Shellscript that enumerates the system configuration and high-level summary of the checks/tasks performed by LinEnum.
```
  git clone https://github.com/rebootuser/LinEnum.git
```

2. Linuxprivchecker (Python 2 ONLY)  
  Enumerates the system configuration and runs some privilege escalation checks as well. It is a python implementation to suggest exploits particular to the system that’s been taken under.
```
  wget http://www.securitysift.com/download/linuxprivchecker.py
```

3. Pentestmonkey - unix-privesc-check (Bash shell script)  
  http://pentestmonkey.net/tools/audit/unix-privesc-check  
    - unix-privesc-check-1.4.tar.gz
    - Not as usefull as some others.

4. Linprivchecker.py  (Python 2 ONLY)
  https://github.com/reider-roque/linpostexp/blob/master/linprivchecker.py  
  - Very complete


5. Linux Privilege Checker (Python 2 ONLY)   
  https://github.com/sleventyeleven/linuxprivchecker
  - Basically the same script as 4.



#### Exploit Suggestions

1. Linux Exploit Suggester 2
    - "Next-generation exploit suggester based on Linux_Exploit_Suggester. This program performs a ‘uname -r‘ to grab the Linux operating system release version, and returns a list of possible exploits."
    - Requires Perl run on attacker machine and few kernel information
  ```
  git clone https://github.com/jondonas/linux-exploit-suggester-2.git
  ```

2. Linux_Exploit_Suggester - InteliSecureLabs  
  https://github.com/InteliSecureLabs/Linux_Exploit_Suggester  



### Escaping Restrictive shells:
  https://speakerdeck.com/knaps/escape-from-shellcatraz-breaking-out-of-restricted-unix-shells  
  https://pen-testing.sans.org/blog/2012/06/06/escaping-restricted-linux-shells  
  https://linuxshellaccount.blogspot.co.za/2008/05/restricted-accounts-and-vim-tricks-in.html  



## Vulnerable Images
* https://in.security/lin-security-practise-your-linux-privilege-escalation-foo/
