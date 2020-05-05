# Directory Permissions

## Checking for folder permissions

**AccessChk** -> tools by SysInternals

* Find weak directories
 ```
 c:> accesschk.exe -uwdqs users c:\
 c:> accesschk.exe -uwdqs "Authenticated Users" c:\

 c:> accesschk.exe -qwsud "Everyone" *
 c:> accesschk.exe -qwsud "Authenticated Users" *
 c:> accesschk.exe -qwsud "Users" *
 ```

* Find weak files
 ```
 c:> accesschk.exe -qwsu "Everyone" *
 c:> accesschk.exe -qwsu "Authenticated Users" *
 c:> accesschk.exe -qwsu "Users" *
 ```

**Cacls / ICacls**
   ```
   c:> cacls "c:\Program Files" /T | findstr Users
   ```

## Program Files & System32
* Being able to write to folders means being able to overwrite files that would be run by user's with higher privilege levels.

* Files in **"Program Files"** folder can only be read / write by privileged users. If you can replace binary in this folder, you can get admin to execute like IE / explorer.exe.  

  Full Permissions for Everyone or Users on Program Folders?
  ```
  c:> icacls "C:\Program Files\*" 2>nul | findstr "(F)" | findstr "Everyone"
  c:> icacls "C:\Program Files (x86)\*" 2>nul | findstr "(F)" | findstr "Everyone"

  c:> icacls "C:\Program Files\*" 2>nul | findstr "(F)" | findstr "BUILTIN\Users"
  c:> icacls "C:\Program Files (x86)\*" 2>nul | findstr "(F)" | findstr "BUILTIN\Users"
  ```

  Modify Permissions for Everyone or Users on Program Folders?
  ```
  c:> icacls "C:\Program Files\*" 2>nul | findstr "(M)" | findstr "Everyone"
  c:> icacls "C:\Program Files (x86)\*" 2>nul | findstr "(M)" | findstr "Everyone"

  c:> icacls "C:\Program Files\*" 2>nul | findstr "(M)" | findstr "BUILTIN\Users"
  c:> icacls "C:\Program Files (x86)\*" 2>nul | findstr "(M)" | findstr "BUILTIN\Users"

  pws> Get-ChildItem 'C:\Program Files\*','C:\Program Files (x86)\*' | % { try { Get-Acl $_ -EA SilentlyContinue | Where {($_.Access|select -ExpandProperty IdentityReference) -match 'Everyone'} } catch {}}
  pws> Get-ChildItem 'C:\Program Files\*','C:\Program Files (x86)\*' | % { try { Get-Acl $_ -EA SilentlyContinue | Where {($_.Access|select -ExpandProperty IdentityReference) -match 'BUILTIN\Users'} } catch {}}
  ```

* Should always check if you can write in **SYSTEM32**. (see the commands above)


## Finding files that are run at startup, outside of "Program Files"
* **Autoruns** tool in Sysinternals  
  tells you everything that runs when user logs in. Looking for something that sits outside of Program Files (since this is not accessible to my user), but is run by admin.

* For instance **bginfo.exe**, this is run by many hosts to display hostname, username etc.  
  bginfo.exe sits in c:\bginfo
  ```
  C:\bginfo>cacls Bginfo.exe
  C:\bginfo\Bginfo.exe  BUILTIN\Administrators:F
                        NT AUTHORITY\SYSTEM:F
                        VMXPSP2\Administrator:F
                        BUILTIN\Users:R
  ```
  This means that the installer prevented other users from overwriting the binary.

  ```
  C:\bginfo>cacls c:\bginfo
  c:\bginfo BUILTIN\Administrators:(OI)(CI)F
            NT AUTHORITY\SYSTEM:(OI)(CI)F
            VMXPSP2\Administrator:F
            CREATOR OWNER:(OI)(CI)(IO)F
            BUILTIN\Users:(OI)(CI)R
            BUILTIN\Users:(CI)(special access:)
                              FILE_APPEND_DATA
            BUILTIN\Users:(CI)(special access:)
                              FILE_WRITE_DATA
  ```
  This means that any user can append / write a file in that directory (but not overwrite the bginfo.exe)   
  There are other ways to circumvent execution if you have write privileges in directory.

  Download the bginfo.exe and run ProcMon.exe (also from Sysinternals) to see what DLL's the exe uses. Windows DLL load hierarchy will look for it in local dir first (c:\bginfo) and if not found will search c:\windows\system32. If it is not in local and we have write privs, one can replace that DLL.



## Incorrect Permissions for Software run by higher privileged users
* Incorrect permissions --> Directly overwrite the binary
  For instance pcAnywhere had incorrect permissions set:
  ```
  C:\Program Files\Symantec\pcAnywhere\awhost32.exe
    Everyone: (OI)(CI)F
    NT AUTHORITY\SYSTEM: (OI)(CI)F

  C:\Program Files\Symantec\pcAnywhere\awrem32.exe
    ...
  ```

  * On newly created directories:
  ```
  C:\>ver
  Microsoft Windows XP [Version 5.1.2600]
  C:\>cacls \testperms
  C:\testperms  BUILTIN\Administrators:(OI) (CI) F
                NT AUTHORITY\SYSTEM:(OI) (CI) F
                VMXPSP2\Administrator:F
                CREATOR OWNDER:(OI) (CI) (IO) F
                BUILTIN\Users:(OI) (CI) R
                BUILTIN\Users:(CI) (special access:) FILE_APPEND_DATA
                BUILTIN\Users:(CI) (special access:) FILE_WRITE_DATA
  ```
  This means that in Windows XP, any user can add to existing file and can create new file (FILE_APPEND_DATA & FILE_WRITE_DATA) even though the user is not the administrator.

  ```
  C:\>ver
  Microsoft Windows [Version 6.1.7600]
  C:\>cacls \testperms
  C:\testperms  BUILTIN\Administrators:(ID) F
                BUILTIN\Administrators:(OI) (CI) (IO) (ID) F
                NT AUTHORITY\SYSTEM:(ID) F
                NT AUTHORITY\SYSTEM:(OI) (CI) (IO) (ID) F
                BUILTIN\Users:(OI) (CI) (ID) R
                NT AUTHORITY\Authenticated Users: (ID) C
                NT AUTHORITY\Authenticated Users: (OI) (CI) (IO) (ID) C
  ```
  Similarly, on Windows 7 any authenticated users can create, append, delete on default directory.

  **How to use this:**  
  User Brett creates new folder, but 'testuser' can create files in this folder.
  ```
  C:\testperms>echo testing > test.txt
  C:\testperms>dir /q
  Directory of C:\testperms
  19/11/2011  12:01 p.m.  <DIR> hidden\Brett.
  19/11/2011  12:01 p.m.  <DIR> NTSERVICE\TrustedInsta..

  19/11/2011  12:01 p.m.  hidden\testuser   test.txt
                1 Files(s)              10 bytes
                2 Dir(s)    35,323,899,904 bytes free
  ```

  So anyone can create an executable (or overwrite one) and have the owner run that file.  

  --> Windows 7 Authenticated users get the following rights:  
  ```
  c:> accesschk.exe -qwv \testperms\admin.txt
  RW NT AUTHORITY\Authenticated Users
      FILE_APPEND_DATA
      FILE_EXECUTE
      FILE_READ_ATTRIBUTES
      FILE_READ_DATA
      FILE_READ_EA
      FILE_WRITE_ATTRIBUTES
      FILE_WRITE_DATA
      FILE_WRITE_EA
      DELETE
      SYNCHRONIZE
      READ_CONTROL
  ```


#### Also check for
* Orphaned install - applications not installed that still exist in startup.
* replacing unknown dlls
* PATH directories with weak permissions - overwrites possible?
