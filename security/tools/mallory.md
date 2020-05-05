# Mallory
Mallory - MiTM TCP and UDP Proxy  
https://github.com/intrepidusgroup/mallory

### Setup
https://www.paladion.net/blogs/thick-client-testing-toolkit-part-3-tools-testing-techniques-interception-testing  
There are multiple ways to set up Mallory; however, the easiest and the most reasonable way while testing would be with the help of two virtual machines by enabling the NAT mode. These virtual machines will use the host machine’s internet connection.

* VM Machine 1: where the Thick Client application is installed.
* VM Machine 2: where Mallory is installed with two NICs, viz. eth0 and eth1.


#### VM Machine 1:
This machine should be configured in such a way that the gateway of this machine is the eth0 interface of Machine 2 and DNS server is the eth1 interface of Machine 2.

* Linux network file
```
auto lo
iface lo inet loopback
auto eth1
iface eth1 inet static
address 1892.168.157.130
netmask 255.255.255.0
network 192.168.157.0
broadcast 192.168.157.255
# Gateway is set to Mallory’s machine IP address
gateway 192.168.157.129
```
* Linux DNS Name in /etc/resolv.conf
```
Google’s public DNS would do the job

8.8.8.8
```


#### VM Machine 2:
This machine should be a Linux machine with two network interfaces (e.g., eth0 and eth1). Out of these two, one should be listening as a gateway interface. There is no special configuration required for this; we have to just assign a static IP address to this interface. The second interface should be running a DNS server to resolve the requested domains, which can be done by starting the DNSMASQ service on eth1.



### Kali Installation

* Mallory installed in /root/mallory
* Code from GitHub in ~/tools/mallory

* To use mallory, open a new terminal window, cd to /root/mallory/current/src, then run:
```
  sudo python ./mallory.py
```

* To run the mallory GUI open a new terminal window cd to /root/mallory/current/src and run:
```
  sudo chown root mallory.log
```
  where root is the user you are logged in as. Then run:
```
  python ./launchgui.py
```

* Edit /etc/network/interfaces
```
auto lo
iface lo inet loopback

iface eth0 inet static
address 10.0.0.51
netmask 255.255.255.0

auto eth1
iface eth1 inet dhcp

iface eth2 inet static
address 10.0.0.53
255.255.255.0
```
