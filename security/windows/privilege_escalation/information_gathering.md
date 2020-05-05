# Initial Information Gathering

## Tools

1. FuzzySecurity - **wmic_info.bat**  
  "Uses WMIC to extract the following information: processes, services, user accounts, user groups, network interfaces, Hard Drive information, Network Share information, installed Windows patches, programs that run at startup, list of installed software, information about the operating system and timezone."  
  http://www.fuzzysecurity.com/coding.html  
  ```
  c:\1.wmic_info> wmic_info.bat
  ```
  *This script outputs the information above to a HTML file, out.html*  

2. PentestMonkey - **windows-privesc-check**
  "Windows-privesc-check is standalone executable that runs on Windows systems. It tries to find misconfigurations that could allow local unprivileged users to escalate privileges to other users or to access local apps (e.g. databases)."  
    https://github.com/pentestmonkey/windows-privesc-check  
    ```
    c:\2.pentestmonkey_wpc> windows-privesc-check2.exe -h    # for help
    c:\2.pentestmonkey_wpc> windows-privesc-check2.exe --audit -a -o {results-filename}
    ```
    *The windows-privesc-check2.exe can be used to output all potential privilege escalation information*

3. **Windows Privesc Check 2.0**  
  "This is a fork of Pentestmonkeys Windows Privesc Check. Here we add support for Windows versions with funny characters and try to clean up the code."   
  https://github.com/silentsignal/wpc/tree/wpc-2.0  
  https://github.com/silentsignal/wpc
  - Build an executable
  ```
  3.wpc# pyinstaller -F windows-privesc-check.py
  ...
  8051 INFO: Building EXE from out00-EXE.toc completed successfully.
  ```
  - copy `windows-privesc-check.exe` from `dist` and run:
  ```
  3.wpc> windows-privesc-check.exe -a
  ```
  Saved to *privesc-report-{machine-name}.html*


4. Harmj0y - **PowerUp**  
  "PowerUp aims to be a clearinghouse of common Windows privilege escalation vectors that rely on misconfigurations."    
  http://www.harmj0y.net/blog/powershell/powerup-a-usage-guide/  
  https://github.com/PowerShellMafia/PowerSploit/tree/master/Privesc  
  https://github.com/PowerShellEmpire/PowerTools/tree/master/PowerUp  

  - To install this module, drop the entire Privesc folder into one of your module directories. The default PowerShell module paths are listed in the $Env:PSModulePath environment variable.

    ```
    pws> $Env:PSModulePath
      C:\Users\IEUser\Documents\WindowsPowerShell\Modules;C:\Windows\system32\WindowsPowerShell\v1.0\Modules\

    c:\4.powerup> cd C:\Users\IEUser\Documents\WindowsPowerShell\Modules
    c:\Users\IEUser\Documents\WindowsPowerShell\Modules> mkdir Privesc

    c:\4.powerup> cp -r .\Privesc C:\Users\IEUser\Documents\WindowsPowerShell\Modules
    ```
    **OR**
    ```
    pws> cd C:\Users\IEUser\Documents\
    pws> mkdir WindowsPowerShell\Modules
    pws> cd WindowsPowerShell\Modules
    pws> cp -r E:\win_priv_esc\4.powerup\Privesc .
    ```
    Then import the module:
    ```
    pws> Import-Module Privesc
    ```
    To check if the module has been imported
    ```
    pws> Get-Module -ListAvailable
    ```
    If the above didn't work, then import with the following
    ```
    pws> Import-Module -Name Privesc
    pws> Get-Command -Module Privesc           # to check
    ```

  - It is possible that powershell scripting has been disabled, then as an *Administrator*:
    ```
    pws> Set-ExecutionPolicy RemoteSigned
    ```

  - If PowerShell scripts are allowed, you can list the commands for this module
    ```
    pws> Get-Command -Module Privesc
    ```

  - To run all privilege escalation checks (output is to screen).
    ```
    pws> Invoke-AllChecks
    ```

5. 411Hall - **JAWS** [Just Another Windows (Enum) Script]
  - Run from within CMD shell and write out to file.
  ```
  c:\5.wpc-ps> powershell.exe -ExecutionPolicy Bypass -File .\jaws-enum.ps1 -OutputFilename JAWS-Enum.txt
  ```

  - Run from within CMD shell and write out to screen.
  ```
  c:\5.wpc-ps> powershell.exe -ExecutionPolicy Bypass -File .\jaws-enum.ps1
  ```

  - Run from within PS Shell and write out to file.
  ```
  pws> .\jaws-enum.ps1 -OutputFileName Jaws-Enum.txt
  ```

6. Session Gopher  
SessionGopher is a PowerShell tool that uses WMI to extract saved session information for remote access tools such as WinSCP, PuTTY, SuperPuTTY, FileZilla, and Microsoft Remote Desktop. It can be run remotely or locally

## Manual Procedure  

* Get the system information and the hostname
  ```
  c:> hostname
  c:> systeminfo
```

* List environment variables
  ```
  c:> set
  c:> echo %PATH%

  pws> Get-ChildItem Env: | ft Key,Value
  ```

* Any connected drives?
  ```
  c:> net use
  c:> wmic logicaldisk get caption,description,providername

  pws> Get-PSDrive | where {$_.Provider -like "Microsoft.PowerShell.Core\FileSystem"}| ft Name,Root
  ```

* Who am I / What user am I?
  ```
  c:> whoami
  c:> whoami /priv
  c:> echo %username%

  pws> $env:UserName
  ```

* What users / localgroups are on the machine?
  ```
  c:> net users

  pws> Get-LocalUser | ft Name,Enabled,LastLogon
  pws> Get-ChildItem C:\Users -Force | select Name
  ```

* What other users are logged on?
  ```
  c:> qwinsta
  ```

* Any old profiles not cleaned up?
  ```
  c:> dir /b /ad "C:\Users\"
  c:> dir /b /ad "C:\Documents and Settings\" # Windows XP and below
  ```

* More info about a specific user / Check if user has privileges.
  ```
  c:> net user user1
  ```

* What local groups are on the system
  ```
  c:> net localgroup

  pws> Get-LocalGroup | ft Name
  ```

* Which users are part of the Administrators group?
  ```
  c:> net localgroup Administrators

  pws> Get-LocalGroupMember Administrators | ft Name, PrincipalSource
  ```

* View Domain Groups
  ```
  c:> net group /domain
  ```

* View Members of Domain Group
  ```
  c:> net group /domain <Group Name>
  ```

* Anything in registry for users Autologon?
  ```
  c:> reg query "HKLM\SOFTWARE\Microsoft\Windows NT\Currentversion\Winlogon" 2>nul | findstr "DefaultUserName DefaultDomainName DefaultPassword"

  pws> Get-ItemProperty -Path 'Registry::HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\WinLogon' | select "Default*"
  ```

* Anything interesting in Credential Manager?
  ```
  c:> cmdkey /list
  c:> dir C:\Users\username\AppData\Local\Microsoft\Credentials\
  c:> dir C:\Users\username\AppData\Roaming\Microsoft\Credentials\

  pws> Get-ChildItem -Hidden C:\Users\username\AppData\Local\Microsoft\Credentials\
  pws> Get-ChildItem -Hidden C:\Users\username\AppData\Roaming\Microsoft\Credentials\
  ```

* Can we access SAM and SYSTEM files?
  ```
  %SYSTEMROOT%\repair\SAM
  %SYSTEMROOT%\System32\config\RegBack\SAM
  %SYSTEMROOT%\System32\config\SAM
  %SYSTEMROOT%\repair\system
  %SYSTEMROOT%\System32\config\SYSTEM
  %SYSTEMROOT%\System32\config\RegBack\system
  ```

* What software is installed?
  ```
  c:> dir /a "C:\Program Files"
  c:> dir /a "C:\Program Files (x86)"
  c:> reg query HKEY_LOCAL_MACHINE\SOFTWARE

  pws> Get-ChildItem 'C:\Program Files', 'C:\Program Files (x86)' | ft Parent,Name,LastWriteTime
  pws> Get-ChildItem -path Registry::HKEY_LOCAL_MACHINE\SOFTWARE | ft Name
  ```

* Check Folder Permissions  
  [Directory Permissions](directory_permissions.md) - directory_permissions.md  


* What internal processes are running that are not accessible from the outside --> Port Forwarding  
  [Services only available from inside network](internal_services.md) - internal_services.md

* Any weak service permissions?
* Are there any unquoted service paths?
  [Services](services.md) - services.md  

* Check **Scheduled Tasks**
  [Scheduled Tasks & Jobs](scheduled_tasks.md) - scheduled_tasks.md

* What is run at startup?
  ```
  c:> wmic startup get caption,command
  c:> reg query HKLM\Software\Microsoft\Windows\CurrentVersion\Run
  c:> reg query HKLM\Software\Microsoft\Windows\CurrentVersion\RunOnce
  c:> reg query HKCU\Software\Microsoft\Windows\CurrentVersion\Run
  c:> reg query HKCU\Software\Microsoft\Windows\CurrentVersion\RunOnce
  c:> dir "C:\Documents and Settings\All Users\Start Menu\Programs\Startup"

  c:> dir "C:\Documents and Settings\%username%\Start Menu\Programs\Startup"

  pws> Get-CimInstance Win32_StartupCommand | select Name, command, Location, User | fl
  pws> Get-ItemProperty -Path 'Registry::HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Run'
  pws> Get-ItemProperty -Path 'Registry::HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\RunOnce'
  pws> Get-ItemProperty -Path 'Registry::HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Run'
  pws> Get-ItemProperty -Path 'Registry::HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\RunOnce'
  pws> Get-ChildItem "C:\Users\All Users\Start Menu\Programs\Startup"
  pws> Get-ChildItem "C:\Users\$env:USERNAME\Start Menu\Programs\Startup"
  ```
* Is AlwaysInstallElevated enabled?
  ```
  c:> reg query HKCU\SOFTWARE\Policies\Microsoft\Windows\Installer /v AlwaysInstallElevated
  ```

* Is the firewall turned on? If so what’s configured?
  ```

  c:> netsh firewall show state
  c:> netsh firewall show config
  c:> netsh advfirewall firewall show rule name=all
  c:> netsh advfirewall export "firewall.txt"
  ```
  - The `netsh` firewall commands are only available from XP SP2 and upwards

* Network
  ```
  # What NICs are connected? Are there multiple networks?
  c:> ipconfig /all
  pws> Get-NetIPConfiguration | ft InterfaceAlias,InterfaceDescription,IPv4Address
  pws> Get-DnsClientServerAddress -AddressFamily IPv4 | ft

  # What routes do we have?
  c:> route print
  pws> Get-NetRoute -AddressFamily IPv4 | ft DestinationPrefix,NextHop,RouteMetric,ifIndex

  # Anything in the ARP cache?
  c:> arp -A
  pws> Get-NetNeighbor -AddressFamily IPv4 | ft ifIndex,IPAddress,LinkLayerAddress,State

  # Are there connections to other hosts?
  c:> netstat -ano

  # Anything in the hosts file?
  C:\WINDOWS\System32\drivers\etc\hosts

  # Any other interesting interface configurations?
  c:> netsh dump

  # Are there any SNMP configurations?
  c:> reg query HKLM\SYSTEM\CurrentControlSet\Services\SNMP /s
  pws> Get-ChildItem -path HKLM:\SYSTEM\CurrentControlSet\Services\SNMP -Recurse
  ```
  - `route print` prints the routing table
  - `arp -A` displays the ARP (Address Resolution Protocol) cache table for all available interfaces.


* List 3rd party drivers. This can be useful sometimes as some 3rd party drivers, even by reputable companies, contain more holes than Swiss cheese. This is only possible because ring0 exploitation lies outside most peoples expertise.
  ```
  c:> DRIVERQUERY
  ```
