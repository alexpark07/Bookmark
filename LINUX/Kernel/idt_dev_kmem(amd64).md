```
Obtain sys_call_table on amd64 (x86_64)

|=-----------------=[ Obtain sys_call_table on amd64(x86_64) ]=---------------=|
|=----------------------------------------------------------------------------=|
|=-----------------------=[ pouik  <pouik@zeppoo.net> ]=----------------------=|


--[ Summary
	1 - Introduction
	2 - IDT
	3 - System_call
	4 - Sys_call_table
	5 - Test
	6 - Conclusion

--[ 1 Introduction

Some people remember the article written by sd & devik in phrack 58 [1] which
introduce for the first time (or it's silvio?) the way to find the 
sys_call_table on X86 in userland with /dev/(k)mem and how to use it, in order 
to create rootkits on Linux.

With this article you'll be able to obtain the sys_call_table on archi Amd64

Most Linux distributions have its kernel compiled with option 
CONFIG_IA32_EMULATION=y which, simply ensure the compatibility between
architecture 32 bits and 64 bits.

Kernel developers' have inserted the ia32_sys_call_table and this is very
interesting... so ladies and gentlemens go to next part ! in order to know 
why :p


--[ 2 IDT

On x86, we can obtain IDT (Interrupt Descriptor Table) with asm instruction
asm("sidt %0" : "=m" (idtr)); which fill the structure idtr : 
	struct {
	        unsigned short limit;
	        unsigned int base;
	} __attribute__((packed)) idtr;


On amd64, the base needs 8 octects (and not 4 octets), so we use the next 
structure :
	struct {
	        unsigned short limit;
	        unsigned long base;
	} __attribute__((packed)) idtr;

--[ 3 System_call

Then, the idea of sd & devik was to obtain the IDT corresponding to
int $0x80 :
	readkmem (&idt,idtr.base+8*0x80,sizeof(idt));
	sys_call_off = (idt.off2 << 16) | idt.off1;


Here that's ok, but we must read 16 bits:
	readkmem (&idt,idtr.base+16*0x80,sizeof(idt));
	sys_call_off = (idt.off2 << 16) | idt.off1;

Take a moment, in the introduction, we indicate that kernel developers have
inserted a compatibility with software 32 bits, and here, the syscall should be
on ia32 :
	promethee kernel # pwd
	/usr/src/linux/arch/x86_64/kernel
	promethee kernel # grep SYSCALL_VECTOR *
	i8259.c:                if (vector != IA32_SYSCALL_VECTOR)
	io_apic.c:      if (current_vector == IA32_SYSCALL_VECTOR)
	traps.c:        set_system_gate(IA32_SYSCALL_VECTOR, ia32_syscall);

So bingo, we obtain the system_call which is,in reality, ia32_syscall. :)

--[ 4 Sys_call_table 


Now that we have ia32_syscall, keep a close eye on execution:

(gdb) disassemble ia32_syscall
Dump of assembler code for function ia32_syscall:
0xffffffff8021f6fc <ia32_syscall+0>:    swapgs 
0xffffffff8021f6ff <ia32_syscall+3>:    sti    
0xffffffff8021f700 <ia32_syscall+4>:    mov    %eax,%eax
0xffffffff8021f702 <ia32_syscall+6>:    push   %rax
0xffffffff8021f703 <ia32_syscall+7>:    cld    
0xffffffff8021f704 <ia32_syscall+8>:    sub    $0x48,%rsp
0xffffffff8021f708 <ia32_syscall+12>:   mov    %rdi,0x40(%rsp)
0xffffffff8021f70d <ia32_syscall+17>:   mov    %rsi,0x38(%rsp)
0xffffffff8021f712 <ia32_syscall+22>:   mov    %rdx,0x30(%rsp)
0xffffffff8021f717 <ia32_syscall+27>:   mov    %rcx,0x28(%rsp)
0xffffffff8021f71c <ia32_syscall+32>:   mov    %rax,0x20(%rsp)
0xffffffff8021f721 <ia32_syscall+37>:   mov    %gs:0x10,%r10
0xffffffff8021f72a <ia32_syscall+46>:   sub    $0x1fd8,%r10
0xffffffff8021f731 <ia32_syscall+53>:   orl    $0x2,0x14(%r10)
0xffffffff8021f736 <ia32_syscall+58>:   testl  $0x181,0x10(%r10)
0xffffffff8021f73e <ia32_syscall+66>:   jne    0xffffffff8021f768 <ia32_tracesys>
End of assembler dump.

(gdb) disassemble ia32_tracesys 
Dump of assembler code for function ia32_tracesys:
0xffffffff8021f768 <ia32_tracesys+0>:   sub    $0x30,%rsp
0xffffffff8021f76c <ia32_tracesys+4>:   mov    %rbx,0x28(%rsp)
0xffffffff8021f771 <ia32_tracesys+9>:   mov    %rbp,0x20(%rsp)
0xffffffff8021f776 <ia32_tracesys+14>:  mov    %r12,0x18(%rsp)
0xffffffff8021f77b <ia32_tracesys+19>:  mov    %r13,0x10(%rsp)
0xffffffff8021f780 <ia32_tracesys+24>:  mov    %r14,0x8(%rsp)
0xffffffff8021f785 <ia32_tracesys+29>:  mov    %r15,(%rsp)
0xffffffff8021f789 <ia32_tracesys+33>:  movq   $0xffffffffffffffda,0x50(%rsp)
0xffffffff8021f792 <ia32_tracesys+42>:  mov    %rsp,%rdi
0xffffffff8021f795 <ia32_tracesys+45>:  callq  0xffffffff8020c684 <syscall_trace_enter>
0xffffffff8021f79a <ia32_tracesys+50>:  mov    0x30(%rsp),%r11
0xffffffff8021f79f <ia32_tracesys+55>:  mov    0x38(%rsp),%r10
0xffffffff8021f7a4 <ia32_tracesys+60>:  mov    0x40(%rsp),%r9
0xffffffff8021f7a9 <ia32_tracesys+65>:  mov    0x48(%rsp),%r8
0xffffffff8021f7ae <ia32_tracesys+70>:  mov    0x58(%rsp),%rcx
0xffffffff8021f7b3 <ia32_tracesys+75>:  mov    0x60(%rsp),%rdx
0xffffffff8021f7b8 <ia32_tracesys+80>:  mov    0x68(%rsp),%rsi
0xffffffff8021f7bd <ia32_tracesys+85>:  mov    0x70(%rsp),%rdi
0xffffffff8021f7c2 <ia32_tracesys+90>:  mov    0x78(%rsp),%rax
0xffffffff8021f7c7 <ia32_tracesys+95>:  mov    (%rsp),%r15
0xffffffff8021f7cb <ia32_tracesys+99>:  mov    0x8(%rsp),%r14
0xffffffff8021f7d0 <ia32_tracesys+104>: mov    0x10(%rsp),%r13
0xffffffff8021f7d5 <ia32_tracesys+109>: mov    0x18(%rsp),%r12
0xffffffff8021f7da <ia32_tracesys+114>: mov    0x20(%rsp),%rbp
0xffffffff8021f7df <ia32_tracesys+119>: mov    0x28(%rsp),%rbx
0xffffffff8021f7e4 <ia32_tracesys+124>: add    $0x30,%rsp
0xffffffff8021f7e8 <ia32_tracesys+128>: jmpq   0xffffffff8021f740 <ia32_do_syscall>
End of assembler dump.

(gdb) disassemble ia32_do_syscall 
Dump of assembler code for function ia32_do_syscall:
0xffffffff8021f740 <ia32_do_syscall+0>: cmp    $0x13c,%eax
0xffffffff8021f745 <ia32_do_syscall+5>: ja     0xffffffff8021f7ed <ia32_badsys>
0xffffffff8021f74b <ia32_do_syscall+11>:        mov    %edi,%r8d
0xffffffff8021f74e <ia32_do_syscall+14>:        mov    %ebp,%r9d
0xffffffff8021f751 <ia32_do_syscall+17>:        xchg   %ecx,%esi
0xffffffff8021f753 <ia32_do_syscall+19>:        mov    %ebx,%edi
0xffffffff8021f755 <ia32_do_syscall+21>:        mov    %edx,%edx
0xffffffff8021f757 <ia32_do_syscall+23>:        callq  *0xffffffff804f6110(,%rax,8)
End of assembler dump.

After visiting ia32_tracesys, we jump on ia32_di_syscall, which makes a call :
callq  *0xffffffff804f6110(,%rax,8)

promethee linux # grep ffffffff804f6110 /boot/System.map
ffffffff804f6110 R ia32_sys_call_table

And that work, we obtain the ia32_sys_call_table :p
In order to obtain it from system_call, sd & devik read 256 characters and 
search pattern "\xff\x14\x85":
	        readkmem (sc_asm,sys_call_off,CALLOFF);
		p = (char*)memmem (sc_asm,CALLOFF,"\xff\x14\x85",3);
		sct = *(unsigned*)(p+3);
Why don't apply this method?

(gdb) x/xw ia32_do_syscall+23
0xffffffff8021f757 <ia32_do_syscall+23>:        0x10c514ff

We just search pattern "\xff\x14\xc5" from ia32_syscall.

If you have understood until then, you think, perhaps,there are errors.
Indeed, we normally have a jump to do with ia32_tracesys in order to arrive
in ia32_dosyscall. And it's right... but we can use the fact that these
functions are consecutive in memory for jump tracesys.

We obtain :
	readkmem (sc_asm,sys_call_off,CALLOFF);
	p = (char*)memmem (sc_asm,CALLOFF,"\xff\x14\xc5",3);
	sct = *(unsigned long*)(p+3);
	sct = (sct & 0x00000000ffffffff) | 0xffffffff00000000;	

We apply a mask in order to ignore useless informations.

Bonus Track :
An other way to find ia32_sys_call_table is using the function walk_krstab 
writing in phalanx rootkit because :
	promethee linux # grep ia32_sys_call_table /boot/System.map 
	ffffffff804f6110 R ia32_sys_call_table
	ffffffff8055feb0 r __ksymtab_ia32_sys_call_table
	ffffffff8056c9d0 r __kstrtab_ia32_sys_call_table
For more informations, please look at the previous article.

--[ 5 Test


promethee linux # grep ia32_syscall /boot/System.map
ffffffff8021f6fc T ia32_syscall
ffffffff804f6af8 r ia32_syscall_end
promethee linux # grep ia32_sys_call_table /boot/System.map
ffffffff804f6110 R ia32_sys_call_table
ffffffff8055feb0 r __ksymtab_ia32_sys_call_table
ffffffff8056c9d0 r __kstrtab_ia32_sys_call_table

Try the first method : 

promethee linux # zeppoo -s -d /dev/mem   
Kernel : 2.6.17.11
Kernel : 2.617110
proc : x86_64
KERNEL_START 0xffffffff80000000
KERNEL_END 0xffffffff81000000
PAGE_OFFSET 0xffff810000000000
PAGE_MAX 0xffffffffffffffff
Memory : /dev/mem
IDTR BASE 0xffffffff8067e000 LIMIT 0xfff
idt80: flags = 238 flags=EE sel=10 off1=f6fc off2=8021
SYSTEM_CALL : 0xffffffff8021f6fc
Sys Call Table 0xffffffff804f6110

With the second method :

promethee linux # zeppoo -s -d /dev/mem -m
Kernel : 2.6.17.11
Kernel : 2.617110
proc : x86_64
KERNEL_START 0xffffffff80000000
KERNEL_END 0xffffffff81000000
PAGE_OFFSET 0xffff810000000000
PAGE_MAX 0xffffffffffffffff
Memory : /dev/mem
IDTR BASE 0xffffffff8067e000 LIMIT 0xfff
idt80: flags = 238 flags=EE sel=10 off1=f6fc off2=8021
SYSTEM_CALL : 0xffffffff8021f6fc
????kstrtab = 0xffffffff8056c9d0
??????Sys Call Table 0xffffffff804f6110

--[ 6 Conclusion


So, it's the end of this small article, the source code is avaible on 
http://www.zeppoo.netand on the svn (repertory libzeppoo) 
'svn checkout https://subversion.cru.fr/zeppoo/trunk/ zeppoo'. 

Now that you have the sys_call_table, techniques which work on i386
can work on amd64...:)


Reference :
	[1] http://phrack.org/archives/58/p58-0x07

Thanks :
	Yperite, n0name, tbowan et aryliin pour me supporter :p

Translated by Yperite (aime pas les chauSettes)

"End of your world, the beginning of mine"

# milw0rm.com [2006-10-12]
© Offensive Security 2009-2015
```
