# Identifying Hashes:
http://openwall.info/wiki/john/sample-hashes

* **Use John Jumbo** -- I've not gotten this to work

  ```sh
  tar -xvzf john-1.8.0-jumbo-1.tar.xz
  apt-get install libssl-dev
  cd john-1.8.0-jumbo-1/src/
  ./configure && make

  # $ sudo apt-get install libssl-dev ## THIS DOES NOT WORK ON LATEST VERION OF KALI, RATHER USE:
  $ apt-get install libssl1.0-dev      ## I've not gotten this to work, reverted back to Kali2
  $ wget http://www.openwall.com/john/j/john-1.8.0-jumbo-1.tar.xz
  $ tar -xvf ./john-1.8.0-jumbo-1.tar.xz
  $ cd john-1.8.0-jumbo-1/src
  $ ./configure
  $ make
  ```

## Cracking Hashes

* Reference for installing and getting JtR 1.8.0-Jumbo working: [Crack (ZIP) passwords with John the Ripper](https://www.bytebang.at/Blog/Crack+%28ZIP%29+passwords+with+John+the+Ripper#)

* Show what hashes have been identified (and stored in john.pot)
  ```
  # john --show ~/file_to_crack
  ```

* John with wordlist
  ```
  ./john --format=netntlmv2 -wordlist=/home/ook/tools/wordlists/sp_big.txt /home/ook/tmp/hashes_small.txt
  ```

* John with wordlist using rules
  ```
  ./john --format=netntlmv2 -wordlist=/home/ook/tools/wordlists/sensepost_combined.txt --rules /home/ook/tmp/hashes_small.txt
  ```

* With wordlist and using all the rules
  ```
  ./john --format=netntlmv2 -wordlist=/home/ook/tools/wordlists/sensepost_combined.txt --rules=ALL /home/ook/tmp/hashes_small.txt
  ```

* John with ruleset around username:
  ```
  ./john --single --format=netntlmv2 /home/ook/tmp/hashes_small.txt
  ```

* John with single and wordlist  
  This attempts passwords with rules based off of the wordlist 'password.lst' – This is a new “trick” not documented anywhere
  ```
  ./john --rules=single --format=netntlmv2 -wordlist=/home/ook/tools/wordlists/sensepost_combined.txt /home/ook/tmp/hashes_small.txt//
  ```


* Add new ruleset  
  Added new ruleset from KoreLogic OWASP presentation:
  So in john.conf, add  
  ```
  //[List.Rules:KoreLogicRulesAppend4Num]//
  //c$[0123456789]$[0123456789]$[0123456789]$[0123456789]//
  //$[0123456789]$[0123456789]$[0123456789]$[0123456789]//
  ```

  Now run with:
  ```
  ./john --rules=KoreLogicRulesAppend4Num --format=netntlmv2 -wordlist=/home/ook/tools/wordlists/sensepost_combined.txt /home/ook/tmp/hashes_small.txt//
  ```

* John hastypes:	2015/06/16
```
7z
AFS
agilekeychain
aix-smd5
aix-ssha1
aix-ssha256
aix-ssha512
asa-md5
bcrypt
bfegg
Bitcoin
blackberry-es10
Blockchain
bsdicrypt
chap
Citrix_NS10
Clipperz
cloudkeychain
cq
CRC32
crypt
dahua
descrypt
Django
django-scrypt
dmd5
dmg
dominosec
dragonfly3-32
dragonfly3-64
dragonfly4-32
dragonfly4-64
Drupal7
dummy
dynamic_n
eCryptfs
EFS
eigrp
EncFS
EPI
EPiServer
fde
FormSpring
Fortigate
gost
gpg
HAVAL-128-4
HAVAL-256-3
hdaa
HMAC-MD5
HMAC-SHA1
HMAC-SHA224
HMAC-SHA256
HMAC-SHA384
HMAC-SHA512
hMailServer
hsrp
IKE
ipb2
KeePass
keychain
keyring
keystore
known_hosts
krb4
krb5
krb5-18
krb5pa-md5
krb5pa-sha1
kwallet
LastPass
LM
lotus5
lotus85
LUKS
MD2
md4-gen
md5crypt
md5ns
mdc2
MediaWiki
MongoDB
Mozilla
mscash
mscash2
MSCHAPv2
mschapv2-naive
mssql
mssql05
mssql12
mysql
mysql-sha1
mysqlna
net-md5
net-sha1
nethalflm
netlm
netlmv2
netntlm
netntlm-naive
netntlmv2
nk
nsldap
NT
nt2
o5logon
ODF
Office
oldoffice
OpenBSD-SoftRAID
openssl-enc
OpenVMS
oracle
oracle11
osc
Panama
PBKDF2-HMAC-SHA1
PBKDF2-HMAC-SHA256
PBKDF2-HMAC-SHA512
PDF
PFX
phpass
PHPS
pix-md5
PKZIP
po
postgres
PST
PuTTY
pwsafe
RACF
RAdmin
RAKP
rar
RAR5
Raw-Blake2
Raw-Keccak
Raw-Keccak-256
Raw-MD4
Raw-MD5
Raw-MD5u
Raw-SHA
Raw-SHA1
Raw-SHA1-Linkedin
Raw-SHA1-ng
Raw-SHA224
Raw-SHA256
Raw-SHA256-ng
Raw-SHA384
Raw-SHA512
Raw-SHA512-ng
ripemd-128
ripemd-160
rsvp
Salted-SHA1
sapb
sapg
scrypt
sha1-gen
sha1crypt
sha256crypt
sha512crypt
Siemens-S7
SIP
skein-256
skein-512
skey
Snefru-128
Snefru-256
SSH
SSH-ng
SSHA512
STRIP
SunMD5
sxc
Sybase-PROP
sybasease
tc_aes_xts
tc_ripemd160
tc_sha512
tc_whirlpool
tcp-md5
Tiger
tripcode
VNC
vtp
wbb3
whirlpool
whirlpool0
whirlpool1
WoWSRP
wpapsk
xsha
xsha512
ZIP
```
