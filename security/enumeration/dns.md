# DNS port 53

* Try domain transfer
  ```
  root@kali:~# dnsrecon -d megacorpone.com -t axfr -n [name_server]
  root@kali:~# dnsrecon -d megacorpone.com -t axfr
  root@kali:~# dnsenum zonetransfer.me

  # host -l megacorpone.com [name_server]

  # host -l megacorpone.com ns1.megacorpone.com

  # host -t ns megacorpone.com | cut -d " " -f 4
    ns1.megacorpone.com.
    ns3.megacorpone.com.
    ns2.megacorpone.com.

  # dig axfr [domain name] @[nameserver]
  # dig axfr friendzone.red @10.10.10.123
  ```

+ Fingerprint server/ service!!!!
  ```
    # host www.megacorpone.com
    # host 50.7.67.155

  Domain Transfer:

    ```

### Tools

* nslookup
  ```
  # nslookup [ -option ... ] [ host-to-find | - [ server ]]
  ```

* dig
  ```
  # dig [ @server ] [-b address ] [-c class ] [-f filename ] [-k filename ] [-p port# ] [-t type ] [-x addr ] [-y name:key ] [-4 ] [-6 ] [name ] [type ] [class ] [queryopt... ]
  ```

* whois-h
  Use the named host to resolve the query -a Use ARIN to resolve the query -r Use RIPE to resolve the query -p Use APNIC to resolve the query -Q Perform a quick lookup

## DNS Enumeration
  dnsenum

## Examine Configuration Files
    host.conf
    resolv.conf
    named.conf
