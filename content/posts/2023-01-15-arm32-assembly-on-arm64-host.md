---
layout: post
title:  "ARM32 Assembly on ARM64 Host"
date:   2023-01-15 14:05:00 +0000
---

Trying to get a ARM32 host to run 32bit assembly is harder than I expected. This is how to run 32bit assembly on a ARM64 bit host.

First install some cross compilation tools.

```
sudo apt install gcc-arm-linux-gnueabihf binutils-arm-linux-gnueabihf binutils-arm-linux-gnueabihf-dbg gcc gdb-multiarch
```

Now trying to assemble the following 32 bit example, that just prints "Hello World" will fail with the `as` command.

```asm
.section .text
.global _start

_start:
/* syscall write(int fd, const void *buf, size_t count) */
    mov r0, #1
    ldr r1, =msg
    ldr r2, =len
    mov r7, #4
    svc #0

/* syscall exit(int status) */
    mov r0, #0
    mov r7, #1
    svc #0

msg:
.ascii "Hello, World!\n"
len = . - msg
```

```
$ as asm32.s -o asm32.o
asm32.s: Assembler messages:
asm32.s:6: Error: operand 1 must be an integer register -- `mov r0,#1'
asm32.s:7: Error: operand 1 must be an integer register -- `ldr r1,=msg'
asm32.s:8: Error: operand 1 must be an integer register -- `ldr r2,=len'
asm32.s:9: Error: operand 1 must be an integer register -- `mov r7,#4'
asm32.s:13: Error: operand 1 must be an integer register -- `mov r0,#0'
asm32.s:14: Error: operand 1 must be an integer register -- `mov r7,#1'
```

But, using the new installed tools, it is possible to assemble the 32 bit assembly on a 64 bit host.

```
$ arm-linux-gnueabihf-as asm32.s -o asm32.o && arm-linux-gnueabihf-ld asm32.o -o asm32

$ file ./asm32 && ./asm32
./asm32: ELF 32-bit LSB executable, ARM, EABI5 version 1 (SYSV), statically linked, not stripped
Hello, ARM32!
```

And possible to debug the program using `gdb-multiarch`.

```
$ gdb-multiarch -q ./asm32
Reading symbols from ./asm32...(no debugging symbols found)...done.
(gdb) br _start
Breakpoint 1 at 0x10054
(gdb) r
Starting program: /home/debian/asm32

Breakpoint 1, 0x00010054 in _start ()
(gdb) disassemble
Dump of assembler code for function _start:
=> 0x00010054 <+0>: mov r0, #1
   0x00010058 <+4>: ldr r1, [pc, #36] ; 0x10084 <msg+16>
   0x0001005c <+8>: ldr r2, [pc, #36] ; 0x10088 <msg+20>
   0x00010060 <+12>: mov r7, #4
   0x00010064 <+16>: svc 0x00000000
   0x00010068 <+20>: mov r0, #0
   0x0001006c <+24>: mov r7, #1
   0x00010070 <+28>: svc 0x00000000
End of assembler dump.
```
