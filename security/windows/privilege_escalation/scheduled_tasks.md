# Scheduled Tasks and Jobs

### Legacy AT command

* `AT` command, runs as SYSTEM - WinNT and Windows 2000, XP as well. The “AT” command is a tool that is used to schedule tasks in Windows. By default, in earlier versions of the Windows operating system the “AT” command was run as SYSTEM. As a result, users can gain access to a console with SYSTEM access when they schedule the cmd.exe as a task.
  ```
  > AT
  ```
  https://www.windows-commandline.com/windows-at-command-examples/

* If you have a GUI with a user that is included in Administrators group you first need to open up cmd.exe for the administrator. If you open up the cmd that is in Accessories it will be opened up as a normal user. And if you right click and do `Run as Administrator` you might need to know the Administrators password. Which you might not know.
* So instead you open up the cmd from `c:\windows\system32\cmd.exe`. This will give you a cmd with Administrators rights.

* From here we want to become SYSTEM user. To do this we run:
  ```
  # First we check what time it is on the local machine:
  time

  # Now we set the time we want the system CMD to start. Probably one minuter after the time.
  at 01:23 /interactive cmd.exe
  ```
  For more on the `at` command, check out [Scheduled Tasks](scheduled_tasks.md)

### Scheduled Tasks

* `Scheduled tasks` - can run as user.  
  Here we are looking for tasks that are run by a privileged user, and run a binary that we can overwrite. So, find a task pointing to an insecure location, replace the binary and when the task is run, the binary will be executed as SYSTEM.

* What scheduled tasks are there? Anything custom implemented?

  ```
  c:> schtasks /query /fo LIST /v    ** See note below **   OR
  c:> schtasks /query /fo LIST 2>nul | findstr TaskName
  c:> dir C:\windows\tasks

  pws> Get-ScheduledTask | where {$_.TaskPath -notlike "\Microsoft*"} | ft TaskName,TaskPath,State
  ```
  ** NOTE: This might produce a huge amount of text. I have not been able to figure out how to just output the relevant strings with *findstr*. So if you know a better way please notify me. As for now I just copy-paste the text and past it into my linux-terminal.
  ```
  cat schtask.txt | grep "SYSTEM\|Task To Run" | grep -B 1 SYSTEM
  ```  

* `compmgmt.msc` Starts the task manager.

* Viewing tasks
  ```
  c:\windows\tasks
  c:\windows\system32\tasks
  ```
