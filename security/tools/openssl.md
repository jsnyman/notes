# OpenSSL


Error when OpenSSL does not want to make a connection:
```
*39647967614624:error:14077438:SSL routines:SSL23_GET_SERVER_HELLO:tlsv1 alert internal error:s23_clnt.c:769:*
```

This issue is well known in several openssl versions, and a bug has been addressed for Ubuntu repositories:
https://bugs.launchpad.net/ubuntu/+source/openssl/+bug/1475228  

If you’re facing it while using openssl directly, you can fix it by specifying the servername on command-line:
```
# openssl s_client -connect www.mywebsite.com:443 -servername www.mywebsite.com
```
https://www.bggofurther.com/2016/06/opensslpyopenssl-ssl23_get_server_hellotlsv1-alert-internal-error/
