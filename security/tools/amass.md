# Amass
https://github.com/OWASP/Amass

In-depth DNS Enumeration and Network Mapping  

### Docker Image

* Mine installed on Ubuntu 18.04-64-bit VM image


* Build
```
sudo docker build -t amass https://github.com/OWASP/Amass.git
```

* Run
```
sudo docker run amass --passive -d example.com      # this according to the documentation, didn't work

$ sudo docker run amass enum -d trustwave.com       # this is how i got it to run
```

* The wordlists maintained in the Amass git repository are available in /wordlists/ within the docker container. For example, to use all.txt:
```
sudo docker run amass -w /wordlists/all.txt -d example.com
```


### Precompiled
* Downloaded to ~/tools/amass
