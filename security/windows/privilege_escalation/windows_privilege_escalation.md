# Windows Privilege Escalation
---  


**TODO:**  
~https://github.com/netbiosX/Checklists/blob/master/Windows-Privilege-Escalation.md~  
https://attack.mitre.org/tactics/TA0004/  
https://github.com/Arvanaghi/SessionGopher  
https://medium.com/@rahmatnurfauzi/windows-privilege-escalation-scripts-techniques-30fa37bd194  


https://labs.mwrinfosecurity.com/assets/1089/original/Windows_Services_-_All_roads_lead_to_SYSTEM-1.1-oct15.pdf
https://sec.mn/Archive/2016/April-Windows_Priv_Esc.pdf
https://toshellandback.com/2015/11/24/ms-priv-esc/

----


No matter what environment you are testing there are going to be a range or roles with varying privileges, for the most part on a local windows environment there going to be three roles / privileged users.

1. System
2. (Local) Administrator
3. Regular user

---
#### Important References:  
* [Windows Privilege Escalation Fundamentals](http://www.fuzzysecurity.com/tutorials/16.html)
* [Encyclopaedia Of Windows Privilege Escalation - Brett Moore](https://www.youtube.com/watch?v=kMG8IsCohHA)
* [Windows Privilege Escalation - a cheatsheet](http://it-ovid.blogspot.cl/2012/02/windows-privilege-escalation.html)

Use these steps when doing manually:
- https://www.absolomb.com/2018-01-26-Windows-Privilege-Escalation-Guide/
- https://www.sock-raw.org/wiki/doku.php/windows_priv_esc
- https://github.com/netbiosX/Checklists/blob/master/Windows-Privilege-Escalation.md

---

## [Kernel Exploits](kernel_exploits.md)
kernel_exploits.md

---
## [Basic Enumeration of the System](information_gathering.md)
information_gathering.md  

---
## [Stored Credentials](stored_credentials.md)
stored_credentials.md

* In Files and Registry

---
## [Services Only Available from Inside Network / Host](internal_services.md)
internal_services.md

  Sometimes there are services that are only accessible from inside the network

---

## GUI Attacks

* **Windows XP / 2003**: Check the taskbar (systray) for apps running as system.
* Open the app, get file dialog "Open" -> Choose cmd.exe -> Get cmd.exe prompt as SYSTEM

---

## Shatter Attacks

* Windows XP / 2003
* Anything running as SYSTEM with a window
* Can be attacked from command line
* Anything that runs with a:
  - Listview / Treeview
  - RichTextBox
  - EditBox

* Directory listing as SYSTEM
  - Open Utility Manager (Windows key + U)
  - Run LBDIR command
    "Add a list of file names to a list box."

  See [Exploiting design flaws in the Win32 API for privilege escalation](https://www.helpnetsecurity.com/2002/08/08/exploiting-design-flaws-in-the-win32-api-for-privilege-escalation-shatter-attacks-how-to-break-windows/)

---
## [Directory Permissions](directory_permissions.md)
directory_permissions.md

---
## [DLL Hijacking](dll_hijacking.md)
dll_hijacking.md

---
## [Scheduled Tasks](scheduled_tasks.md)
scheduled_tasks.md

---
## [Services](services.md)
services.md

---
## [Registry](registry.md)
registry.md


---
## Group Policy Preference

* Group policy preferences allows domain admins to create and deploy across the domain local users and local administrators accounts. *This feature was introduced in Windows 2008 Server*.
* It can be abused by an attacker since the credentials of these accounts are stored encrypted and the public key is published by Microsoft. This leaves the door open to any user to retrieve these files and decrypt the passwords stored in order to elevate access.

* If the machine belongs to a domain and your user has access to `System Volume Information` there might be some sensitive files there.

* First we need to map/mount that drive. In order to do that **we need to know the IP-address of the domain controller**. We can just look in the environment-variables

  ```
  # Output environment-variables
  set

  # Look for the following:
  LOGONSERVER=\\NAMEOFSERVER
  USERDNSDOMAIN=WHATEVER.LOCAL

  # Look up ip-addres
  nslookup nameofserver.whatever.local

  # It will output something like this
  Address:  192.168.1.101

  # Now we mount it
  net use z: \\192.168.1.101\SYSVOL

  # And enter it
  z:

  # Now we search for the groups.xml file
  dir Groups.xml /s
  ```

* If we find the file with a password in it, we can decrypt it like this in Kali
  ```
  gpp-decrypt encryptedpassword
  ```

* Or with [this tool](http://www.sec-1.com/blog/wp-content/uploads/2015/05/gp3finder_v4.0.zip)

* Or with Metasploit:
```
post/windows/gather/credentials/gpp
```

* PowerSploit
```
Get-CachedGPPPassword //For locally stored GP Files
Get-GPPPassword //For GP Files stored in the DC
```

---
## MSIExec method
* Execute a MSI file to execute our payload as SYSTEM.
* Use the WIX framework to create an install file, which executes the payload.

* The [WIX Framework](http://wixtoolset.org/) is made up of several tools, but the two that are used
  1. candle.exe - Takes a .WIX XML file and outputs a .WIXOBJ
  2. light.exe - Takes a .WIXOBJ and creates a .MSI

* Wixgen file: https://gist.githubusercontent.com/xpn/d1ef20dfd266053227d3e992ae84c64e/raw/cfa59045475060990093b097f72717d3fd500f88/msigen.wix
* So, when executed:
  1. Our first custom action will be launched, forcing our payload to run as the SYSTEM account.
  2. Our second custom action will be launched, causing some invalid VBScript to be executed and stop the install process with an error.

* To compile this into a MSI we save the above contents as a file called "msigen.wix", and use the following commands:
  ```
  candle.exe msigen.wix
  torch.exe msigen.wixobj
  ```

* Finally execute the MSI file.

See also [Alternative methods of becoming SYSTEM](https://blog.xpnsec.com/becoming-system/)

---
## PROC_THREAD_ATTRIBUTE_PARENT_PROCESS method

```
New-Win32Process cmd.exe -CreationFlags Newconsole -ParentProcess (Get-NtProcess -Name lsass.exe)
```

---
## Token Impersonation

* Every process has an identity token that identifies the user that started the process or the privileges that it is running under.
* Here he mentions that if you have compromised the box and have access to restricted user, and you have access to an images folder under the IIS webroot, then you can create an ASPX which you can use to priv esc by kidnapping a token to get local system.

* What is impersonation?   
  The ability of a thread to execute using a different security token

* Requires SeImpersonatePrivilege  
  The following accounts still have this privilege:
    - ASPNET, IWAM_computername
    - Local Service, Network Service

* Token Reading
  - Cesar Cerrudo - Token Kidnapping 1/2/3 (Churrasco)
  - MWR InfoSecurity - Whitepaper

---

## NamedPipe Impersonation (ImpersonateNamePipe)

* Fist Advisory:
    ```
    Advisory Name:  Named Pipe Filename Local Privilege Escalation
    Release Date:   07/08/2003
    Application:    Microsoft SQL Server
    Platform:       Windows NT / 2000 / XP
    Severity:       Local Privilege Escalation
    ```
    This is functionality abuse, no vulnerability, impersonation.

* **Metasploit** uses this technique to escalate privileges.
* A named pipe is a mechanism that enables interprocess communication for applications to communicate locally or remotely.
* The application that creates the pipe is known as the pipe server, and the application that connects to the pipe is known as the pipe client.

* Exploit can be found [here](https://github.com/Cn33liz/EasySystem)

#### References
- [Identifying Named Pipe Impersonation and Other Malicious Privilege Escalation Techniques](https://securityintelligence.com/identifying-named-pipe-impersonation-and-other-malicious-privilege-escalation-techniques/)
- [Discovering and Exploiting Named Pipe Security Flaws for Fun and Profit](http://www.blakewatts.com/namedpipepaper.html)
- [Alternative methods of becoming SYSTEM](https://blog.xpnsec.com/becoming-system/)


---
# Local Admin -> SYSTEM

## [Dump Hashes](dump_hashes.md)
dump_hashes.md


#### On Windows XP and Older
* Check out the `at` command, in [Scheduled Tasks](scheduled_tasks.md)


#### Vista and Newer

* You first need to upload [PsExec.exe](https://docs.microsoft.com/en-us/sysinternals/downloads/psexec) and then you run:
  ```
  psexec -i -s cmd.exe
  ```
* See also [PsExec and the Nasty Things It Can Do](http://techgenix.com/PsExec-Nasty-Things-It-Can-Do/)

#### Binary replacement
  1. Stop service
  2. Replace binary
  3. Start service
  Usually works for Power Users too!!


#### Kitrap
* On some machines the `at 20:20` trick does not work. It never works on Windows 2003 for example. Instead you can use Kitrap. Upload both files and execute vdmaillowed.exe. I think it only works with GUI.
  ```
  vdmallowed.exe
  vdmexploit.dll
  ```

## Admin -> Domain Account

From local admin to (potentially) domain admin.

* Incognito  
If an attacker is able to get SYSTEM level access to a workstation, for example by compromising a local administrator account, and a Domain Administrator account is logged in to that machine then it may be possible for the attacker to simply read the administrator’s access token in memory and steal it to allow them to impersonate that account.
  - luke_jennings
  - Standalone of Metasploit
  - Finds usable delegation tokens

  *TIP:* File servers are virtual treasure troves of tokens since most file servers are used as network attached drives via domain logon scripts.  
  See also [MetaSploit Unleashed - Fun with Incognito](https://www.offensive-security.com/metasploit-unleashed/fun-incognito/)  
  [MWR Labs - Incognito 2.0](https://labs.mwrinfosecurity.com/blog/incognito-v2-0-released/)

* Impersonate  
  Snarf anyone's token from running processes

* Process Injection  
  Administrator can hijack any user's process

* WCE  
  http://www.ampliasecurity.com/research.html

  - Improved 'Pass the hash'
    Retrieves hashes from LSASS, modifies in memory current user hashes
  - Steal once use many account.
