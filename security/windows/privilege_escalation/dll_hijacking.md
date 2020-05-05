## DLL Hijacking
http://www.greyhathacker.net/?p=738

### Using tools:
https://pentestlab.blog/2017/04/04/dll-injection/  
* PowerSploit, Metasploit.
* [Remote DLL Injector](https://securityxploded.com/remote-dll-injector.php)
* [Dll Injector v2](https://www.dllinjector.com/)

### Search Paths

* If a program or service loads a file from a directory we have write access to we can abuse that to pop a shell with the privileges the program runs as.

* Generally a Windows application will use pre-defined search paths to find DLL's and it will check these paths in a specific order. DLL hijacking usually happens by placing a malicious DLL in one of these paths while making sure that DLL is found before the legitimate one.

* DLL Search order for a 32-bit systems:
  1. The directory from which the application loaded
  2. 32-bit System directory (C:\Windows\System32)
  3. 16-bit System directory (C:\Windows\System)
  4. Windows directory (C:\Windows)
  5. The current working directory (CWD)
  6. Directories in the system PATH environment variable.
  7. Directories in the user PATH environment variable.


* Search Path - Directories with weak permissions. For instance if something like Perl is installed and it sets itself in the PATH. If the installation directory has weak permissions, you can replace / add DLL here.  File doesn't exist in system32

* **Steps to exploitation**
  1. List all the processes on the system and discover these processes which are running as SYSTEM and are missing DLL’s.  
  *Process Monitor* will identify if there is any DLL that the application tries to load and the actual path that the application is looking for the missing DLL.

  2. Find folder with weak permissions where malicious DDL could be injected. By default if a software is installed on the C:\ directory instead of the C:\Program Files then authenticated users will have write access on that directory. Additionally software like Perl, Python, Ruby etc. usually are added to Path variable. This give the opportunity of privilege escalation since the user can write a malicious DLL in that directory which is going to be loaded the next time that the process will restart with the permission of that process.

  3. Create malicious DLL (msfvenom).
  ```
  # msfvenom
  ```

* **PowerSploit**
  - The module *Find-ProcessDLLHijack* will identify all the processes on the system that are trying to load DLL’s which are missing.
  - The module *Find-PathDLLHijack* will identify paths that the user can modify the content.
  - The module *Write-HijackDll* will write the (seperately created) malicious DLL.

### DLL Redirection

* Mechanism is introduced in Windows 2000. For an application foo.exe, if there is a file foo.exe.local exists, Windows will first look at foo.exe’s application directory, before start the regular dll search.

* In Windows XP and Windows Server 2003, the behavior of DotLocal(.local) is altered a little bit. If foo.exe.local is a file, Windows will keep the same behavior as Windows 2000. But if foo.exe.local is a directory, Windows will search foo.exe.local directory for the dll, instead of the application directory.

* See [Dynamin-Link Library Redirection](https://docs.microsoft.com/en-us/windows/desktop/Dlls/dynamic-link-library-redirection) or https://blogs.msdn.microsoft.com/junfeng/2006/01/24/dotlocal-local-dll-redirection/

* To use DLL redirection, create a redirection file for your application. The redirection file must be named as follows: App_name.local. For example, if the application name is Editor.exe, the redirection file should be named Editor.exe.local. You must install the .local file in the application directory. You must also install the DLLs in the application directory.

* Can set the DLL to use. This is done by specifying `.local` or in the case of .NET applications `.manifest`. - If the application has a manifest, then any .local files are ignored.

* Known DLL's cannot be redirected
  The common system dll's (KnownDLLs reg key)

* Here are Windows Services that have been found to be vulnerable and could be exploited on Windows 7 (32/64)
  - IKE and AuthIP IPsec Keying Modules (IKEEXT) – wlbsctrl.dll
  - Windows Media Center Receiver Service (ehRecvr) – ehETW.dll
  - Windows Media Center Scheduler Service (ehSched)– ehETW.dll

* Windows XP
  - Automatic Updates (wuauserv) – ifsproxy.dll
  - Remote Desktop Help Session Manager (RDSessMgr) – SalemHook.dll
  - Remote Access Connection Manager (RasMan) – ipbootp.dll
  - Windows Management Instrumentation (winmgmt) – wbemcore.dll
* Other Services that might be installed are also vulnerable
  - Audio Service (STacSV) – SFFXComm.dll SFCOM.DLL
  - Intel(R) Rapid Storage Technology (IAStorDataMgrSvc) – DriverSim.dll
  - Juniper Unified Network Service(JuniperAccessService) – dsLogService.dll
  - Encase Enterprise Agent – SDDisk.dll

* No dll hijacking vulnerabilities were found on a clean default installation of Windows 8 OS (64).
