---
layout: post
title:  "IOLI Crackmes"
date:   2020-01-29 14:00:00 +0000
---

Lets download them

```shell
wget https://github.com/radareorg/radare2book/raw/master/crackmes/ioli/IOLI-crackme.tar.gz \
  && tar xvzf ./IOLI-crackme.tar.gz
```

## crackme0x00

First lets see what happens when its ran.

```
% ./crackme0x00

IOLI Crackme Level 0x00
Password: 
Invalid Password!
```

Lets see where `Password:` is in the `strings` of the binary.

```
% strings ./crackme0x00 | grep -B1 -A1 Password

IOLI Crackme Level 0x00
Password: 
250382
Invalid Password!
Password OK :)
GCC: (GNU) 3.4.6 (Gentoo 3.4.6-r2, ssp-3.4.6-1.0, pie-8.7.10)
```

Alright, there is a number there, lets use that as the password.

```
%echo "250382" | ./crackme0x00

IOLI Crackme Level 0x00
Password: 
Password OK :)
```

## crackme0x01

Moving on, lets see how this one behaves when executed.

```
% ./crackme0x01

IOLI Crackme Level 0x01
Password: 
Invalid Password!
```

Like last time lets looks at the =strings= of the binary.

```
% strings ./crackme0x01
```

Not so lucky this time, lets dig in a bit more with `radare2`

```python
import r2pipe

f = "crackme0x01"
r = r2pipe.open(f)
r.cmd("aaa")
print(r.cmd("pdf@main"))
```

```
   / (fcn) main 113
   |   main (int argc, char **argv, char **envp);
   |           ; var unsigned int local_4h @ ebp-0x4
   |           ; var int local_4h_2 @ esp+0x4
   |           ; DATA XREF from entry0 (0x8048347)
   |           0x080483e4      55             push ebp
   |           0x080483e5      89e5           mov ebp, esp
   |           0x080483e7      83ec18         sub esp, 0x18
   |           0x080483ea      83e4f0         and esp, 0xfffffff0
   |           0x080483ed      b800000000     mov eax, 0
   |           0x080483f2      83c00f         add eax, 0xf
   |           0x080483f5      83c00f         add eax, 0xf
   |           0x080483f8      c1e804         shr eax, 4
   |           0x080483fb      c1e004         shl eax, 4
   |           0x080483fe      29c4           sub esp, eax
   |           0x08048400      c70424288504.  mov dword [esp], str.IOLI_Crackme_Level_0x01 ; [0x8048528:4]=0x494c4f49 ; "IOLI Crackme Level 0x01\n" ; const char *format
   |           0x08048407      e810ffffff     call sym.imp.printf         ; int printf(const char *format)
   |           0x0804840c      c70424418504.  mov dword [esp], str.Password: ; [0x8048541:4]=0x73736150 ; "Password: " ; const char *format
   |           0x08048413      e804ffffff     call sym.imp.printf         ; int printf(const char *format)
   |           0x08048418      8d45fc         lea eax, dword [local_4h]
   |           0x0804841b      89442404       mov dword [local_4h_2], eax
   |           0x0804841f      c704244c8504.  mov dword [esp], 0x804854c  ; [0x804854c:4]=0x49006425 ; const char *format
   |           0x08048426      e8e1feffff     call sym.imp.scanf          ; int scanf(const char *format)
   |           0x0804842b      817dfc9a1400.  cmp dword [local_4h], 0x149a
   |       ,=< 0x08048432      740e           je 0x8048442
   |       |   0x08048434      c704244f8504.  mov dword [esp], str.Invalid_Password ; [0x804854f:4]=0x61766e49 ; "Invalid Password!\n" ; const char *format
   |       |   0x0804843b      e8dcfeffff     call sym.imp.printf         ; int printf(const char *format)
   |      ,==< 0x08048440      eb0c           jmp 0x804844e
   |      ||   ; CODE XREF from main (0x8048432)
   |      |`-> 0x08048442      c70424628504.  mov dword [esp], str.Password_OK_: ; [0x8048562:4]=0x73736150 ; "Password OK :)\n" ; const char *format
   |      |    0x08048449      e8cefeffff     call sym.imp.printf         ; int printf(const char *format)
   |      |    ; CODE XREF from main (0x8048440)
   |      `--> 0x0804844e      b800000000     mov eax, 0
   |           0x08048453      c9             leave
   \           0x08048454      c3             ret

```

Here is the disassembly of the `main` function of the binary, notice the `cmp dword [local_4h], 0x149a` at `0x0804842b`. This looks to be comparing `0x149a`, but `0x149a` is hex.

Using `radare2` again, it can give us all other numeric values for `0x149a`

```
r2 -c "? 0x149a" --

hex     0x149a
octal   012232
unit    5.2K
segment 0000:049a
int32   5274
string  "\x9a\x14"
binary  0b0001010010011010
fvalue: 5274.0
float:  0.000000f
double: 0.000000
trits   0t21020100
```

Now that we have all other numeric values for `0x149a` lets try that `int32` number.

```
% echo "5274" | ./crackme0x01

IOLI Crackme Level 0x01
Password: 
Password OK :)
```

## crackme0x02

Lets start things off with looking at the output of =strings=.

```
% strings ./crackme0x02
```

Like last time nothing jumps out as potentially being the password, Lets review the `main` function in `radare2` again.

```python
import r2pipe

 f = "crackme0x02"
 r = r2pipe.open(f)
 r.cmd("aaa")
 print(r.cmd("pdf@main"))
```

```
   / (fcn) main 144
   |   main (int argc, char **argv, char **envp);
   |           ; var unsigned int local_ch @ ebp-0xc
   |           ; var signed int local_8h @ ebp-0x8
   |           ; var int local_4h @ ebp-0x4
   |           ; var int local_4h_2 @ esp+0x4
   |           ; DATA XREF from entry0 (0x8048347)
   |           0x080483e4      55             push ebp
   |           0x080483e5      89e5           mov ebp, esp
   |           0x080483e7      83ec18         sub esp, 0x18
   |           0x080483ea      83e4f0         and esp, 0xfffffff0
   |           0x080483ed      b800000000     mov eax, 0
   |           0x080483f2      83c00f         add eax, 0xf
   |           0x080483f5      83c00f         add eax, 0xf
   |           0x080483f8      c1e804         shr eax, 4
   |           0x080483fb      c1e004         shl eax, 4
   |           0x080483fe      29c4           sub esp, eax
   |           0x08048400      c70424488504.  mov dword [esp], str.IOLI_Crackme_Level_0x02 ; [0x8048548:4]=0x494c4f49 ; "IOLI Crackme Level 0x02\n" ; const char *format
   |           0x08048407      e810ffffff     call sym.imp.printf         ; int printf(const char *format)
   |           0x0804840c      c70424618504.  mov dword [esp], str.Password: ; [0x8048561:4]=0x73736150 ; "Password: " ; const char *format
   |           0x08048413      e804ffffff     call sym.imp.printf         ; int printf(const char *format)
   |           0x08048418      8d45fc         lea eax, dword [local_4h]
   |           0x0804841b      89442404       mov dword [local_4h_2], eax
   |           0x0804841f      c704246c8504.  mov dword [esp], 0x804856c  ; [0x804856c:4]=0x50006425 ; const char *format
   |           0x08048426      e8e1feffff     call sym.imp.scanf          ; int scanf(const char *format)
   |           0x0804842b      c745f85a0000.  mov dword [local_8h], 0x5a  ; 'Z' ; 90
   |           0x08048432      c745f4ec0100.  mov dword [local_ch], 0x1ec ; 492
   |           0x08048439      8b55f4         mov edx, dword [local_ch]
   |           0x0804843c      8d45f8         lea eax, dword [local_8h]
   |           0x0804843f      0110           add dword [eax], edx
   |           0x08048441      8b45f8         mov eax, dword [local_8h]
   |           0x08048444      0faf45f8       imul eax, dword [local_8h]
   |           0x08048448      8945f4         mov dword [local_ch], eax
   |           0x0804844b      8b45fc         mov eax, dword [local_4h]
   |           0x0804844e      3b45f4         cmp eax, dword [local_ch]
   |       ,=< 0x08048451      750e           jne 0x8048461
   |       |   0x08048453      c704246f8504.  mov dword [esp], str.Password_OK_: ; [0x804856f:4]=0x73736150 ; "Password OK :)\n" ; const char *format
   |       |   0x0804845a      e8bdfeffff     call sym.imp.printf         ; int printf(const char *format)
   |      ,==< 0x0804845f      eb0c           jmp 0x804846d
   |      ||   ; CODE XREF from main (0x8048451)
   |      |`-> 0x08048461      c704247f8504.  mov dword [esp], str.Invalid_Password ; [0x804857f:4]=0x61766e49 ; "Invalid Password!\n" ; const char *format
   |      |    0x08048468      e8affeffff     call sym.imp.printf         ; int printf(const char *format)
   |      |    ; CODE XREF from main (0x804845f)
   |      `--> 0x0804846d      b800000000     mov eax, 0
   |           0x08048472      c9             leave
   \           0x08048473      c3             ret
```

Here it looks it moves a value into the `eax` register at runtime and then jumps if not equal to the value, lets try to find that value of the `eax` register at runtime.

```
% echo "test" | \
  r2 -AAA ./crackme0x02 -d -c "db 0x0804844b" -c "dc" -c "dr"

eax = 0x00052b24
ebx = 0x00000000
ecx = 0x00000000
edx = 0x000001ec
esi = 0xf7ee0000
edi = 0xf7ee0000
esp = 0xff9c4410
ebp = 0xff9c4438
eip = 0x0804844b
eflags = 0x00000206
oeax = 0xffffffff

```

Now to get the `int32` value of the `eax` register value.

```
% r2 -c "? 0x00052b24" -- | grep int32

int32   338724
```

Time to see if this was correct

```
% echo "338724" | ./crackme0x02

IOLI Crackme Level 0x02
Password: 
Password OK :)

```
