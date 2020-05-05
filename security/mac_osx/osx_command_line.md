* Check open ports:
```
# netstat -atp tcp | grep -i "listen"
```

* Flush DNS (after edit of hosts file)
```
sudo dscacheutil -flushcache
```
