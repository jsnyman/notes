# Sudo and Su

* The SU (Switch User) command.
* `su` requires the password of the user.

* The SUDO (Substitute User and Do) command.
* `sudo` requires your password.

* `sudo` and `su` are privilege management software.


## Sudo

* The sudo command allows you to run any command as another user and is commonly used to elevate permissions so that the command is run as an administrator (which in Linux terms is known as the root user).

* The rules considering the decision making about granting an access, we can find in `/etc/sudoers` file.

* Access to programs using sudo, you might be able to escalate privileges.
* Any sudo access to a shell gives you root, for example vi, but also bash, dash,...
* Any program that can write or overwrite can be used.  
  For example, if you have sudo-rights to `cp` you can overwrite `/etc/shadow` or `/etc/sudoers` with your own malicious file.

. Sudo requires the password of the current user, su requires the password of the user to be impersonated.

* First thing to check
```
$ sudo i
```

* Checking for sudo:
  ```
  $ sudo -l
  ```
* **/etc/sudoers**

  ```sh
  # cat /etc/sudoers

  root ALL=(ALL:ALL)  ALL
  peter ALL=(ALL) NOPASSWD: STRACE
  ```
    - The line `(ALL) ALL` means that root can run anything when authenticated as root  
    - The line `NOPASSWD: xxx` means that those commands can be run as root without a password.

* If you can run `/sbin/service` without a password, you can start any service as root, so to start ssh:
```
$ sudo /sbin/service sshd start
```

* SEE [Applications that can be used](#applications_abused.md)

* See [GTFOBins](https://gtfobins.github.io/#+sudo): GTFOBins is a curated list of Unix binaries that can be exploited by an attacker to bypass local security restrictions.

#### Add a User to /etc/passwd

1. Privileged access to `adduser`
```
$ adduser user1
```

2. If user has sudo or similar access to programs that can overwrite files, for instance `cp`. It is possible to manually enter user details, with no password set.
```
user2:*:1002:1003:,,,:/home/user2:/bin/bash
```
  Then use `cp` to overwrite the existing file.
```
$ sudo cp mypasswd /etc/passwd
```
---

## Sudo Command

* To invoke a login shell using sudo just use `-i`. When command is not specified you'll get a login shell prompt, otherwise you'll get the output of your command.

Example (login shell):
```
sudo -i
```
Example (with a specified user):
```
sudo -i -u user
```
Example (with a command):
```
sudo -i -u user whoami
```
Example (print user's $HOME):
```
sudo -i -u user echo \$HOME
```
Note: The backslash character ensures that the dollar sign reaches the target user's shell and is not interpreted in the calling user's shell.
