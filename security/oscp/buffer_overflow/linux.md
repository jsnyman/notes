### Linux

+ To test if ASLR is enabled on Linux:

  ```
  $ cat /proc/sys/kernel/randomize_va_space
  2
  ```
  The value 2 means ASLR is enabled

#### EDB - Evans Linux Debugger

  + run the application through the edb
  ```
  root@kali:~# edb --run /usr/games/crossfire/bin/crossfire
  ```
  Need to hit 'Run' twice to have the application run.

  + The strategy is the same:

    1. Crash the application
    2. Control (find) EIP
    3. Finding space in the buffer
    4. Discovering bad characters
    5. Find a return address (JMP ESP)
    6. Adding shellcode in memory

  + Might also have space in buffer in front of EIP.

    - EAX usually points to start of injected buffer
    - Might have some characters at start that needs to be ignored. These characters need
      to be inspected. Sometimes they translate to conditional jumps that do not jump out
      of the execution path required.  
      OR  
      Jump over the first 12 characters of EAX  
      In Assembly, this translates to:  
      `add eax, 12`  
      and then to go to the value in eax:  
      `jmp eax`  

      To get the opcodes for this:  
      ```
      root@kali:~# /usr/share/metasploit-framework/tools/exploit/nasm_shell.rb
      nasm > add eax,12
      00000000  83C00C            add eax,byte +0xc
      nasm > jmp eax
      00000000  FFE0              jmp eax
      nasm >
      ```

      **** THIS is then tacked on after EIP, to redirect the execution

  + Find return address done with "Opcode Search"
  Search for "JMP ESP":<br/>
    - Click on first Region
    - "What to search for" = "Jump Equivalent"; "ESP -> EIP"
#### GDB - Using the GNU debugger

+ To start gdb for a program:
  ```
  # gdb -q validate
  Reading symbols from validate...done.
  ```

+ Now to run that program (with arguments):
  ```
  (gdb) r `perl -e 'print "\x41"x116 . "\x42"x4'`
  Starting program: /usr/local/bin/validate `perl -e 'print "\x41"x116 . "\x42"x4'`

  Program received signal SIGSEGV, Segmentation fault.
  0x42424242 in ?? ()
  ```

+ To check the content of the registers at crash:
  (info registers)
  ```
  (gdb) i r
  eax            0xbf96b718    -1080641768
  ecx            0x0    0
  edx            0x79    121
  ebx            0x41414141    1094795585
  esp            0xbf96b790    0xbf96b790
  ebp            0x41414141    0x41414141
  esi            0x0    0
  edi            0x0    0
  eip            0x42424242    0x42424242
  eflags         0x10286    [ PF SF IF RF ]
  cs             0x73    115
  ss             0x7b    123
  ds             0x7b    123
  es             0x7b    123
  fs             0x0    0
  gs             0x33    51
  (gdb)
  ```

+ Examine the content of the memory location:
  x 0xaddress - Examine the contents of memory.  
  x/nfu 0xaddress -   Examine the contents of memory and specify formatting.  
        n: number of display items to print
        f: specify the format for the output
        u: specify the size of the data unit (eg. byte, word, ...)

  ```
  (gdb) x/10x $eax
  0xbf95fbe8:    0x41414141    0x41414141    0x41414141    0x41414141
  0xbf95fbf8:    0x41414141    0x41414141    0x41414141    0x41414141
  0xbf95fc08:    0x41414141    0x41414141
  (gdb) x/10x $eax -4
  0xbf95fbe4:    0xb75f3eb0    0x41414141    0x41414141    0x41414141
  0xbf95fbf4:    0x41414141    0x41414141    0x41414141    0x41414141
  0xbf95fc04:    0x41414141    0x41414141
  (gdb)
  ```

  Check the stack pointer (ESP):
  ```
  (gdb) x/s $esp
  ```

+ To view the instructions:

  ```
  (gdb) disas main
  ```

  Or a specific memory address:

  ```
  (gdb) disas 0x80484b4
  ```

+ Find specific instructions in memory:
  “objdump” can be used to find the particular instruction to get to your shellcode.

  ```
  # objdump -d validate | grep -i jmp
  ```
