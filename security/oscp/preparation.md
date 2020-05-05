https://www.hackthebox.eu/
https://ringzer0ctf.com/challenges



https://0xdf.gitlab.io/tags.html#hackthebox



TODO:
----

https://backdoorshell.gitbooks.io/oscp-useful-links/content/enumeration.html





* Go through the Vuln ISO's done before.
  Also see these: https://resources.infosecinstitute.com/privilege-escalation-linux-live-examples/  


- More Windows kernel exploits testing
  https://github.com/netbiosX/Checklists/blob/master/Windows-Privilege-Escalation.md  
    --> https://pentestlab.blog/2017/04/24/windows-kernel-exploits/  
  * Checkout compiled exploits: http://www.bhafsec.com/files/windows/



# Vulnhub ISO's:

(#1) Kioptrix: 2014 https://www.vulnhub.com/entry/kioptrix-2014-5,62/
(#2) FristiLeaks: 1.3 https://www.vulnhub.com/entry/fristileaks-13,133/
(#3) Stapler: 1 https://www.vulnhub.com/entry/stapler-1,150/
(#4) VulnOS: 2 https://www.vulnhub.com/entry/vulnos-2,147/
(#5) SickOs: 1.2 https://www.vulnhub.com/entry/sickos-12,144/
(#6) Brainpan: 1 ttps://www.vulnhub.com/entry/brainpan-1,51/
(#7) HackLAB: Vulnix https://www.vulnhub.com/entry/hacklab-vulnix,48/
(#8) /dev/random: scream https://www.vulnhub.com/entry/devrandom-scream,47/
(#9) pWnOS: 2.0 https://www.vulnhub.com/entry/pwnos-20-pre-release,34/
(#10) SkyTower: 1 https://www.vulnhub.com/entry/skytower-1,96/

https://www.abatchy.com/2017/02/oscp-like-vulnhub-vms


* MOre here: https://backdoorshell.gitbooks.io/oscp-useful-links/content/vulnhub-vms.html


Beginner friendly

    Kioptrix: Level 1 (#1)
    Kioptrix: Level 1.1 (#2)
    Kioptrix: Level 1.2 (#3)
    Kioptrix: Level 1.3 (#4)
    FristiLeaks: 1.3
    Stapler: 1
    PwnLab: init

Intermediate

    Kioptrix: 2014
    Brainpan: 1
    Mr-Robot: 1
    HackLAB: Vulnix

Not so sure (Didn’t solve them yet)

    VulnOS: 2
    SickOs: 1.2
    /dev/random: scream
    pWnOS: 2.0
    SkyTower: 1
    IMF

There aren’t many Windows machines around due to licensing. Few options:

    [Hack The Box](https://www.hackthebox.eu/login): Got a nice set of Windows machines from Windows 2000 up to Windows 8.1 I believe.
    [Metasploitable 3](https://github.com/rapid7/metasploitable3/wiki), will download a trial version of Windows Server.
    https://github.com/magnetikonline/linuxmicrosoftievirtualmachines you can download Windows VMs legally then hack your way through them through an unpatched vulnerability or setting up a vulnerable software.
    Set up your own lab. Default Windows XP SP0 will give you the chance to try out a few remote exploits, or doing some privilege escalation using weak services.
    [/dev/random: Sleepy](https://www.vulnhub.com/entry/devrandom-sleepy,123/) (Uses VulnInjector, need to provide you own ISO and key.)
    [Bobby: 1](https://www.vulnhub.com/entry/bobby-1,42/) (Uses VulnInjector, need to provide you own ISO and key.)

If you think something is worth to be added to this list please mention it in the comments, I do check them ;)

- Abatchy
