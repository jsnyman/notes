# PowerShell

* Get the PowerShell version
  ```
  pws> $psversiontable
  ```




## Run a PowerShell script
https://ss64.com/ps/syntax-run.html

* Before running any scripts on a new PowerShell installation, you must first set an appropriate Execution Policy, e.g. Set-ExecutionPolicy RemoteSigned
  ```
  pws> Set-ExecutionPolicy RemoteSigned
  ```

* If you run a script (or even just enter a command) without specifying the fully qualified path name, PowerShell will search for it as follows:
  - Currently defined aliases
  - Currently defined functions
  - Commands located in the system path.


* The most common (default) way to run a script is by calling it:
  ```
  pws> & "C:\Batch\My first Script.ps1"
  pws> & cscript /nologo "C:\Batch\another.vbs"
  ```
  If the path does not contain any spaces, then you can omit the quotes and the '&' operator
  ```
  pws> C:\Batch\Myscript.ps1
  ```
  If the script is in the current directory, you can omit the path but must instead explicitly indicate the current directory using .\ (or ./ will also work)
  ```
  pws> .\Myscript.ps1
  ```

* An alternative which allows running a script (or command) on local or remote computers is **Invoke-Command**
  ```
  pws> invoke-command -filepath c:\scripts\test.ps1 -computerName Server64
  ```
  https://ss64.com/ps/invoke-command.html

* Running scripts **as Admin**:
  - It is possible to right click Powershell.exe (or it's Start menu shortcut) and run it 'As Admin'.

  - To elevate a script from a (non-elevated) PowerShell command line:
  ```
  pws> Start-Process powershell -ArgumentList '-noprofile -file MyScript.ps1' -verb RunAs
  ```

  - A set of commands can also be saved in a scriptblock variable, and then passed to a new (elevated) PowerShell session:
  ```
  pws> Start-Process -FilePath powershell.exe -ArgumentList $code -verb RunAs -WorkingDirectory C:
  ```

  - To run an entire PowerShell session 'As Admin' from an existing PowerShell (non-elevated) session:
  ```
  pws> Start-Process powershell.exe -Verb runAs
  ```

  If you use Invoke-Command to run a script or command on a **remote computer**, then it will not run elevated even if the local session is. This is because any prompt for elevation will happen on the remote machine in a non-interactive session and so will fail.

* To get help on a script
  ```
  pws> get-help .\azucar.ps1
  ```


## Execution Policy
https://ss64.com/ps/set-executionpolicy.html

* Change the user preference for the execution policy of the shell.

  ```
  pws> Set-ExecutionPolicy ...
  ```



## An A-Z Index of Windows PowerShell commands
https://ss64.com/ps/
