# Table of contents
- ## [x and or](#x-and-or)
  
---

# Notes

### I'm using Ubuntu 20.04 as my environment.

---

# x and or
![question screenshot](../images/xandor.png)

## Information
```
./x-and-or

Enter the flag: testtesttest
That is not the flag.
```

## My solution
1. I think i used the most time consuming way to solve this
2. first, i decompile it using ghidra,
3. at 0x12a7, there's a call rcx instruction, which is also the function for checking the flag

![solution 1](../images/xandor_sol_1.png)

4. upon further investigation, the address RCX is pointing to is a piece of instruction code generated while the program is running

![solution 2](../images/xandor_sol_2.png)

5. which it XOR 0x42 for 500 bytes starting from address of ```check_password```

after xor'd with 0x42, the 500 bytes will be a valid assembly instruction
![solution 3](../images/xandor_sol_3.png)

6. but, for some reason i can't properly reverse the flag using the information, so i ended up finding the flag character by character and patch the ```CMP EDX, $some_var``` out to ```\x90\x90```

```
(gdb) break *0x00005555555552a7
Breakpoint 4 at 0x5555555552a7

(gdb) break *0x7ffff7ffb000+0x132
Breakpoint 7 at 0x7ffff7ffb132
(gdb) set write on
(gdb) set *0x7ffff7ffb132 = 0x07749090
(gdb) set *0x7ffff7ffb134 = 0x90909090
(gdb) set *0x7ffff7ffb138 = 0x90909090
(gdb) set *0x7ffff7ffb13c = 0xc8458390
(gdb) cont

Breakpoint 5, 0x00007ffff7ffb132 in ?? ()
(gdb) info register
rax            0x66                102
rbx            0x5555555552f0      93824992236272
rcx            0x0                 0
rdx            0x66                102
.
.
.
(gdb) cont
Continuing.

Breakpoint 7, 0x00007ffff7ffb132 in ?? ()
(gdb) info r
rax            0x43                67
rbx            0x5555555552f0      93824992236272
rcx            0x9                 9
rdx            0x33                51
.
.
.
Breakpoint 7, 0x00007ffff7ffb132 in ?? ()
(gdb) info r
rax            0x43                67
rbx            0x5555555552f0      93824992236272
rcx            0x22                34
rdx            0x34                52
.
.
.
(gdb) cont
Continuing.
That is the flag!!!!
[Inferior 1 (process 208119) exited normally]
(gdb) 
```

## Flag: flag{560637dc0dcd33b5ff37880ca10b24fb}

---