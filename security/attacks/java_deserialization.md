## Java Deserialization

**From Olga Fun Friday mail:**
* Insecure deserialization happens when the type (class) of object to be recreated, is read from client-side and not whitelisted.

* A useful class for using is *com.sun.rowset.JdbcRowSetImpl*.
  - In the JDK
  - Has no params constructor
  - Leads to JNDI resolution when setters for *dataSourceName* and *autoCommit* are called.
  - Server side deserialization leads to *InitialContext.lookup(String)* method execution.

* The String parameter for the *InitialContext.lookup(String)* method can be an LDAP address

https://www.blackhat.com/docs/us-16/materials/us-16-Munoz-A-Journey-From-JNDI-LDAP-Manipulation-To-RCE-wp.pdf

https://blog.gdssecurity.com/labs/2018/4/18/jolokia-vulnerabilities-rce-xss.html
