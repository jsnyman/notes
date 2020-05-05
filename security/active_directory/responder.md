# Responder
https://github.com/SpiderLabs/Responder

* Responder is a LLMNR, NBT-NS and MDNS poisoner,
* with built-in HTTP/SMB/MSSQL/FTP/LDAP rogue authentication server
* supporting NTLMv1/NTLMv2/LMv2, Extended Security NTLMSSP and Basic HTTP authentication.
* Responder can start a WPAD proxy server to intercept all requests.

* **LLMNR** - Link-Local Multicast Name Resolution
  - The Link-Local Multicast Name Resolution (LLMNR) is a protocol based on the Domain Name System (DNS) packet format that allows both IPv4 and IPv6 hosts to perform name resolution for hosts on the same local link. It is included in Windows Vista, Windows Server 2008, Windows 7, Windows 8 and Windows 10.
  - Port: UDP/5355

* **NBT-NS** - NetBIOS over TCP/IP Name Service (NBT-NS)
  - In order to start sessions or distribute datagrams, an application must register its NetBIOS name using the name service.
  - NBT-NS resolves names using network broadcasts, by using a WINS server, or both.
  - Port: UDP/138

* **MDNS** - Multicast DNS (mDNS)
  - mDNS protocol resolves host names to IP addresses within small networks that do not include a local name server.

* An attacker can listen on a network for these LLMNR (UDP/5355) or NBT-NS (UDP/137) broadcasts and respond to them, thus pretending that the attacker knows the location of the requested host.  

* **WPAD** - Web Proxy Auto-Discovery Protocol
  - The Web Proxy Auto-Discovery Protocol (WPAD) is a method used by clients to locate the URL of a configuration file using DHCP and/or DNS discovery methods. Once detection and download of the configuration file is complete, it can be executed to determine the proxy for a specified URL.
  - Victim sends their username and hashed password (in NTLMv2 format)
```
responder -I eth0 --wpad
```
  - Crack any hashes captured here OR
  - Use Ntlmrelayx.py
    - https://hausec.com/how-to-set-up-ntlmrelayx-py/
    - https://github.com/SecureAuthCorp/impacket
