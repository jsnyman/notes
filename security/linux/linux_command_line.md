# Linux Command Line Commands

* What does the **linux pipe symbol** | do?
  If you're comfortable with output and input redirection, the explanation is really quite easy.
```
Command1 | Command2
```
  does the same as
```
Command1 > tempfile
Command2 < tempfile
```

  but without tempfile. The output of Command1 is directly connected to the input of Command2 and the transfer happens in-memory.


## Environment Variables

```
cat /etc/profile
cat /etc/bashrc
cat ~/.bash_profile
cat ~/.bashrc
cat ~/.bash_logout
env
set
```

### Exploiting Bad Path Configuration

* Exploiting users with ‘.’ in their PATH

* Users add '.' in their path to avoid having to type `./some_command`.

* When this happens a malicious file can be created in a folder, user execute it rather than a command they wanted to.

* For instance:
  - The current user has "." in the PATH.
  - A custom script is found that has `suid` permission set (can execute as root).
  - Custom script calls `ps` as part of execution, without specifying the full path.
  - **Exploit** by creating an executable called `ps` in the current folder. This adds `ps` to the PATH.

  ```sh
  echo "/bin/bash" > ps
  chmod 777 ps
  #OR
  ln -s /bin/sh ps

  ./that_custom_script.sh
  whoami
  ```


---
## General

* Commands can be split with a semicolon
```
$ echo hello; whoami
hello
username
```

* To make locate work
```
# updatedb
```

* get my external ip address
  ```
  $ dig +short myip.opendns.com @resolver1.opendns.com
  $ curl www.ipmango.com/api/myip
  ```

* Set Prompt
```
PS1=[\\W]\#
echo $PS1
```
  https://www.cyberciti.biz/tips/howto-linux-unix-bash-shell-setup-prompt.html  
  https://www.cyberciti.biz/faq/bash-shell-change-the-color-of-my-shell-prompt-under-linux-or-unix/  

* Base64 Decode
```
$ base64 -d cABhAHMAcwB3AG8AcgBkAFAAYQBzAHMAdwBvAHIAZAA=
```


*  when a space cannot be added in bash command, use:
  `$IFS`, which is a special way of telling bash to “use the internal field separator”, which is a long-winded way of writing a space.
```
# ls;killall$IFS'httpd'
```

* nameservers are set in `/etc/resolv.conf`

---
## Users and passwords

* Add a user (only root may add a user or group) -> /usr/sbin/
```
# adduser user1
```

* Set the password for an existing user
```
# passwd <username>
Enter new UNIX password:
Retype new UNIX password:
passwd: password updated successfully
```

* Create a password to manually enter in to `/etc/passwd`
```
#  openssl passwd -1 -salt <username> <password>
```

* `etc/passwd` Is a human-readable text file which stores information of user account.
    If you can write to this file you can add a user. NOTE need to add to the group file as well!!
    1. Username
    2. Encrypted Password / None  
      `x` = password stored in /etc/shadow  
      `*` = no password set  
      Anything else is an encrypted password. This password can be created as follows:
      ```
      $ openssl passwd -1 -salt <username> <password>

      $ openssl passwd -1 -salt ook thisismypassword
      $1$ook$52gsEQHLblJ2CoBtCIaaa/
      ```
    3. User Id
    4. Group Id
    5. Gecos Field
    6. Home Directory
    7. Command / Shell

    ```
    firstuser:x:1000:1000:,,,:/home/first:/bin/bash
    user1:*:1001:1001:,,,/home/user1:/bin/bash
    ook:$1$ook$52gsEQHLblJ2CoBtCIaaa/:0:0:,,,/home/ook:/bin/bash
    ```
      - Now we can set the passwd for the user with `passwd` (potentially).

* `etc/group` Is a human-readable text file which stores group information as well as which user belongs to which group.
  ```
  $ vim /etc/group

  # Add the newly created users' groups
  user1:*:1001
  ```

* `etc/shadow` Is a file that contains encrypted password information of the accounts

* For information on `su` and `sudo` see [sudo and su](sudo.md)

* Retrieve all ssh users and passwords (ssh host):
```
$ sudo strace -f -e “read” -p <pid_of_sshd> 2>log
```



---



## Bash Scripting

See also [Bash Guide For Beginners](http://www.tldp.org/LDP/Bash-Beginners-Guide/html/index.html)


* for loop with command
```
# for url in $(cat list.txt); do host $url; done
```
Or not inline:
```
for in in *.png
do
convert $i $i.jpg
done
```

---
## Other tools

* To list all the commands available
```
$ compgen -c
```

### Find

* To delete all empty files in a folder
```
find . -size 0 -delete
```

### Grep

See [Grep](../tools/grep.md)

### Tar
http://osxdaily.com/2012/04/05/create-tar-gzip/  

* To compress
```
tar -cvzf tarballname.tar.gz itemtocompress
```

* To decompress
```
gunzip filename.tar.gz
tar -xvf filename.tar
```

### Proxychains
proxychains - a tool that forces any TCP connection made by any given application to follow through proxy like TOR or any other SOCKS4, SOCKS5 or HTTP(S) proxy. Supported auth-types: "user/pass" for SOCKS4/5, "basic" for HTTP.  
https://github.com/haad/proxychains  

* edit `/etc/proxychains.conf`
* usage
```
$ proxychains telnet host.com
$ proxychains -f /etc/proxychains-other.conf targethost2.com

```
