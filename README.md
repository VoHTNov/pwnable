# pwnable.tw-unexploitable

# IDA
The program in address 0x4005d0 and 0x4005e6 is great for writing values into registers and call any funtion.

                             LAB_004005d0                                   
        004005d0 4c 89 fa        MOV        RDX,R15
        004005d3 4c 89 f6        MOV        RSI,R14
        004005d6 44 89 ef        MOV        EDI,R13D
        004005d9 41 ff 14 dc     CALL       qword ptr [R12 + RBX*0x8]
        004005dd 48 83 c3 01     ADD        RBX,0x1
        004005e1 48 39 eb        CMP        RBX,RBP
        004005e4 75 ea           JNZ        LAB_004005d0
                             LAB_004005e6                                    
        004005e6 48 8b 5c        MOV        RBX,qword ptr [RSP + local_30]
                 24 08
        004005eb 48 8b 6c        MOV        RBP,qword ptr [RSP + local_28]
                 24 10
        004005f0 4c 8b 64        MOV        R12,qword ptr [RSP + local_20]
                 24 18
        004005f5 4c 8b 6c        MOV        R13,qword ptr [RSP + local_18]
                 24 20
        004005fa 4c 8b 74        MOV        R14,qword ptr [RSP + local_10]
                 24 28
        004005ff 4c 8b 7c        MOV        R15,qword ptr [RSP + local_8]
                 24 30
        00400604 48 83 c4 38     ADD        RSP,0x38
        00400608 c3              RET
# Exploit:
  + Via .GOT address call read() to write string "/bin/sh" into any location in the .bss region (in the exploit program I chose bss+0x100).
  + Via .GOT address call read() to write smallest-bit of sleep function in .GOT region a value (value is determined via debug using the library). The purpose is that when calling the sleep() function, the program will execute the syscall command, not step by step according to the sleep() function.
  + Via .GOT address call read() to write a string of length 0x3b to any address in .bss region (as long as it doesn't overwrite the "/bin/sh" string) so that rax will be returned the value 0x3b.
  + Via .GOT address call sleep() (essentially calling syscall because it was overwritten mallest bit of address) and parameter is rdi = "/bin/sh" address to get execuve /bin/sh shell.
