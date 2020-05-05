# Communications & Networking

* NIC's or networks
```
/sbin/ifconfig -a
cat /etc/network/interfaces
cat /etc/sysconfig/network
```

* Network configuration settings
```
cat /etc/resolv.conf
cat /etc/sysconfig/network
cat /etc/networks
iptables -L
hostname
dnsdomainname
```

* What other hosts are communicating with the system?
```
lsof -i
lsof -i :80
grep 80 /etc/services
netstat -antup
netstat -antpx
netstat -tulpn
chkconfig --list
chkconfig --list | grep 3:on
last
w
```

* Cached addresses
```
arp -e
route
/sbin/route -nee
```
