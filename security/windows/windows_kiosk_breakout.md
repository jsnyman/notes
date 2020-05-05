https://www.trustedsec.com/2015/04/kioskpos-breakout-keys-in-windows/

## Method 1:
Get Task Manager (taskmgr.exe) up and use File ->New Task (Run…) File menu item to run the program you want.

## Method 2:
Bring up Windows Explorer in Windows (if it happens to be to an Internet Explorer window, just type in C:\ in the URL bar), then find an EXE you want to run (C:\Windows\System32\ is a good place to look for CMD.exe, Explorer.exe, Taskmgr.exe, regedt32.exe, etc.) and double click it.

## Method 3:
This method uses the fact that by default if you can get to a File Save/Open dialog, you can right click an EXE, then select Open from the right click drop down menu (Do not click Open or Save on the dialog window itself or you may corrupt a file, only on the menu that comes up from right clicking). You may have to set the File Name input box to `*.*` or `*.EXE` and hit enter to see the files you want to Right Click->Open and run. You could also click somewhere in the file selection pane and hit CTRL+N to make a new Explorer window so you don’t have to Right Click->Open. If you can’t right click, try CTRL+Left Click as a substitute or dragging an EXE onto cmd.exe to make it run.

ALT+F4: Quit program
Killing POS/Kiosk software may drop you to normal desktop. I believe this only works if
HKEY_LOCAL_MACHINE\Software\Microsoft\Windows NT\CurrentVersion\WinLogon\Shell
Has not been set to run something other than Explorer as the shell.

ALT+SPACE: System Menu
Displays the main window’s System menu as if you clicked on the icon in the top left of the window. Minimizing or resizing may let you get at what is behind the POS/Kiosk window.

ALT+TAB: Switch between open programs
The current window may not have a way to escape, but if other software is running in the background you may find an escape there.

CTRL+ALT+DEL: Task Manager or Windows Security Screen
Brings up a dialog on which one option is to start Task manager. From there use File ->New Task (Run…) (Method 1).

CTRL+B: Open Book Marks Menu
From here you may be able to right click, open, and get an explorer menu (Method 2).

CTRL+ESC: Opens start menu
From the start menu you may be able to get to someplace else, assuming Explorer is even running as the shell.

CTRL+F4: Closes the current Multiple Document Interface (MDI) window
This way, you may see what is behind the main window.

CTRL+P: May bring up print dialog.
From here, you can click the “Find Printer” button, go to help, and break out via file Menu or Right Click “View Source”. You could also right click on an installed printer and view its help (Method 3, and see F1 section below).

CTRL+SHIFT+ESC: Opens Windows Task Manager
Much like CTRL+ALT+DEL, but skips the step of clicking Task Manager on the security screen. See Method 1.

CTRL+Tab: May close current windows or tab
This way, you may see what is behind the main window.

CTRL+Windows Key+F: Find computer
Brings up an Explorer dialog you can break out of via Method 2.

F1: Starts Windows Help
“Microsoft Windows Help” Classic windows help has File Open menu. “Microsoft HTML Help” based windows might have the option to right click and view source on the selected help. Either way, use Method 3.

F3: May bring up Windows search
Another potential Explorer window to escape from.

SHIFT five times: Toggles Sticky Keys on and off
On newer versions of Windows, this brings you a few clicks away from a help dialog and escape (Method 3). Even if you can’t get to a Help dialog, this feature is still useful. If all you have is remote access, it can be hard to send the proper keystrokes without your host OS interpreting them first. This accessibility feature in Windows allows Sticky Keys (Ctrl, Alt, Shift, Windows Logo) to be pressed once, but have the OS considered the key to be held down. This makes it easier to make some of the other keystroke combinations listed in this article without your host system interpreting the keystrokes first. For example, if you want to try to type CTRL+SHIFT+ESC, your host OS will likely interpret it first, not sending it to your remote session. However, if you hit SHIFT 5 time real fast, the Sticky Keys popup should show up on both your host OS, and in your remote session. Enable Sticky Keys on the remote OS by hitting the OK/Yes button (wording depends on Windows version) on the remote OS but Cancel/No on the host OS. You can then type CTRL (release key), SHIFT (release key), and finally ESC (release key) to have the same effect as CTRL+SHIFT+ESC on the remote system without the host system interpreting the combination first. Another option for remote testers is On Screen Keyboard (osk.exe) but not all keyboard shortcuts work with it.

SHIFT+RIGHT CLICK: Open Command Prompt Here
Only for Vista and newer unless the registry has be modified. Let’s you right click in a folder, on the Desktop, or even a File Open/Save dialog and launch a command prompt using the “Open command window here” option in the drop down.

Windows Logo+Break (AKA:Pause): System Properties dialog box
From here use Method 2.

Windows Logo+D: Minimizes all open windows and displays the desktop
This way, you may see what is behind the main window.

Windows Logo+E: Windows Explorer
Method 2 in a nutshell.

Windows Logo+F1: Windows OS Help
This is the Windows help, not the applications. Use Method 3.

Windows Logo+R: Run dialog box
Run what you want.

Windows Logo+T: Select active application on taskbar
Sort of like ALT+TAB, you may see what is behind the main window this way.

Windows Logo+U: Accessibility Utility Manager
Let you fire up On Screen Keyboard (and other utilities like screen magnifier and narrator), from which you can bring up a help dialog, then break out via Right Click ->View Source then use Notepad to find and EXE, Right Click it and Open (Method 3). You can also use “About” links on each of the apps to fire up a web browser, then use it as an Explorer Window to find an EXE you want to run via Method 1

Windows Logo: Start menu
From the Start menu, you can run what you want.

If you have a Microsoft keyboard and the IntelliType Software Installed you may be able to break out using a help dialog or an Explorer window with these keys:

Windows Logo+P: Starts Print Manager
Windows Logo+C: Opens Control Panel
Windows Logo+V: Starts Clipboard
Windows Logo+K: Opens Keyboard Properties dialog box
Windows Logo+I: Opens Mouse Properties dialog box
Windows Logo+A: Starts Accessibility Options (if installed)
Windows Logo+SPACEBAR: Displays the list of Microsoft IntelliType shortcut keys

That’s it for now, If you know more shortcut/ways to escape kiosk and POS software, please contact me. In a future article I may give a list of recommendations for Windows settings to disable keys. For now, here are some Registry keys to be aware of when locking down a system, in the form of a REG file:
Windows Registry Editor Version 5.00

;Not for the keyboard, but keeps people from using Right Click->Open (does not stop drag and drop)
[HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Policies\Explorer]
“NoViewContextMenu”=dword:00000001

;Disable Tasksk Manage via CTRL+SHIFT+ESC and CTRL+ALT+DEL
[HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Policies\System]
“DisableTaskMgr”=dword:00000001

;Disable most Windows Key functions
[HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\Explorer]
“NoWinKeys”=dword:00000001

;Disable Windows key, as NoWinKeys gets some but not all
[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Keyboard Layout]
“Scancode Map”=hex:00,00,00,00,00,00,00,00,03,00,00,00,00,00,5B,E0,00,00,5C,E0,00,00,00,00

The above is by no means a complete list, and many items may be dependent on the shell you use. Look into the “Scancode Map” one especially, as you can remap or disable any key you like. Keymapper (http://justkeepswimming.net/keymapper/) from Stuart Dunkeld is especially useful for figuring out the codes to use with “Scancode Map”.
If you want to find out more about bypassing captive shells, POS systems or kiosks, here are some other good articles on the subject:

If you have a captive web browser you want to escape out of and have access to type in a URL, check out iKat:
http://ikat.ha.cked.net

Paul Craig’s talk from Defcon 16 is a good resource:
https://www.youtube.com/watch?v=DSu8z3vrfcY

My article focused on keyboard input, for something more general check out Scott Sutherland’s article on breaking out of Kiosks:
https://www.netspi.com/blog/entryid/180/breaking-out-of-applications-deployed-via-terminal-services-citrix-and-kiosks

If you are interested in hacking POS hardware, see Mick Douglas’ “Pwning the POS!” talk from Notacon 11:
http://www.irongeek.com/i.php?page=videos/notacon11/pwning-the-pos-mick-douglas

This blog post was written by Adrian Crenshaw (@irongeek_adc ) of TrustedSec. Thanks to some fine folks on Twitter for suggestions and avenues of research: @huliosstudio @Securithid @sambowne @ZeroCoolRanch ‏@DELICIOUSKEK @ThemsonMester @fjhackett
