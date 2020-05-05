# PsExec

https://docs.microsoft.com/en-us/sysinternals/downloads/psexec
```
Usage: psexec [\\computer[,computer2[,...] | @file\]\]\[-u user [-p psswd][-n s][-r servicename][-h][-l][-s|-e][-x][-i [session]][-c executable [-f|-v]][-w directory][-d][-<priority>][-a n,n,...] cmd [arguments]
```
Parameter |	Description
---       | ---
-a | Separate processors on which the application can run with commas where 1 is the lowest numbered CPU. For example, to run the application on CPU 2 and CPU 4, enter: "-a 2,4"
-c | Copy the specified executable to the remote system for execution. If you omit this option the application must be in the system path on the remote system.
-d | Don't wait for process to terminate (non-interactive).
-e | Does not load the specified account’s profile.
-f | Copy the specified program even if the file already exists on the remote system.
-i | Run the program so that it interacts with the desktop of the specified session on the remote system. If no session is specified the process runs in the console session.
-h | If the target system is Vista or higher, has the process run with the account's elevated token, if available.
-l | Run process as limited user (strips the Administrators group and allows only privileges assigned to the Users group). On Windows Vista the process runs with Low Integrity.
-n | Specifies timeout in seconds connecting to remote computers.
-p | Specifies optional password for user name. If you omit this you will be prompted to enter a hidden password.
-r | Specifies the name of the remote service to create or interact with.
-s | Run the remote process in the System account.
-u | Specifies optional user name for login to remote computer.
-v | Copy the specified file only if it has a higher version number or is newer on than the one on the remote system.
-w | Set the working directory of the process (relative to remote computer).
-x | Display the UI on the Winlogon secure desktop (local system only).
-priority | Specifies -low, -belownormal, -abovenormal, -high or -realtime to run the process at a different priority. Use -background to run at low memory and I/O priority on Vista.
computer | Direct PsExec to run the application on the remote computer or computers specified. If you omit the computer name, PsExec runs the application on the local system, and if you specify a wildcard `(\\*)`, PsExec runs the command on all computers in the current domain.
@file | PsExec will execute the command on each of the computers listed in the file.
cmd | Name of application to execute.
arguments | Arguments to pass (note that file paths must be absolute paths on the target system).
-accepteula | This flag suppresses the display of the license dialog.

### Typical Usage
* Execute an executable on a host, using the provided credentials
```
psexec.exe \\192.168.1.10 -u peter -p [thepassword] /c c:\backdoor.bat
```

* Execute an executable on a host, using the provided username and NTLM hash
```
psexec.exe \\192.168.1.10 -u peter -p [thepasswordhash] /c c:\backdoor.bat
```

* Execute regedit on a host as SYSTEM account
```
psexec.exe \\[IP_ADDRESS] -s -i regedit
```
