## Test for myself

- PsExec
```
psexec.exe \\192.168.1.10 -u peter -p [thepassword] /c c:\backdoor.bat
psexec.exe \\192.168.1.10 -u peter -p [thepasswordhash] /c c:\backdoor.bat
psexec.exe \\[IP_ADDRESS] -s -i regedit
```
  **THEN FINISH WRITING PSEXEC.MD**

- Try the MSIExec method in windows_priv_esc from https://blog.xpnsec.com/becoming-system/

- Play with weak services. Create, search for and exploit

- Test DLL hijacking by creating bad dll and implementing exploit

- Check out https://github.com/samratashok/nishang/tree/master/Escalation
https://github.com/davehardy20/PowerSploit

* Abusing Token Privileges For LPE https://github.com/hatRiot/token-priv/blob/master/abusing_token_eop_1.0.txt


# NEXT in Privilege Escalation:

* Big collection to go through - https://github.com/vitalysim/Awesome-Hacking-Resources/blob/master/README.md#privilege-escalation  
  **ALL WINDOWS LINKS DONE**

* Busy with https://www.slideshare.net/mubix/windows-attacks-at-is-the-new-black-26665607 - slide 36
* Work through this: https://pentestlab.blog/2017/04/24/windows-kernel-exploits/


https://osandamalith.com/2017/03/24/places-of-interest-in-stealing-netntlm-hashes/
https://github.com/decoder-it/whoami-priv/blob/master/whoamipriv.pdf

https://www.youtube.com/watch?v=kMG8IsCohHA&feature=youtu.be  
https://www.youtube.com/watch?v=PC_iMqiuIRQ  

http://pwnwiki.io/#!privesc/windows/uac.md (Chrome only)

https://github.com/netbiosX/Checklists/blob/master/Windows-Privilege-Escalation.md
https://www.sploitspren.com/2018-01-26-Windows-Privilege-Escalation-Guide/
https://hackingandsecurity.blogspot.com/2017/09/oscp-windows-priviledge-escalation.html
https://attack.mitre.org/wiki/Privilege_Escalation
-





# MS Security courses - free!!
* Powershell - https://mva.microsoft.com/en-us/training-courses/getting-started-with-powershell-3-0-jump-start-8276?l=r54IrOWy_2304984382
* Others - https://mva.microsoft.com/training-topics/enterprise-security#!lang=1033

## AD
https://github.com/hausec/ADAPE-Script
https://www.slideshare.net/harmj0y/derbycon-the-unintended-risks-of-trusting-active-directory
https://github.com/vysec/AD-Attack-Defense

## Unclassified
From Workstation to Domain
Admin. https://adsecurity.org/wp-content/uploads/2018/10/2018-DerbyCon-FromWorkstationToDomainAdmin-Metcalf-Final.pdf
https://github.com/byt3bl33d3r/SILENTTRINITY
https://github.com/api0cradle/UltimateAppLockerByPassList/blob/master/README.md
