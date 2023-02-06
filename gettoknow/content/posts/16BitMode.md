---
title: "16BitMode"
date: 2023-02-04T18:08:46+01:00
draft: false
tags: ["OsDev"]
---

``` C
	Our journey starts in 16 bit mode, and the BIOS is the one for the job.
```

# Real Mode:

### `=> Registers and Data:`
```
 In Real Mode we are running in a 16 bit envirement, so the max numbers that a cpu can deal with at the time are 16 bit numbers, in case we needed to use two 32 bit numbers, the number of cycles needed to do the same instruction will double.
 All x86 CPUs have 4 general purpose registers: ax, bx, cx and dx, that can be used to hold a word (two bytes) of data, the cpu can read and write to them faster than accessing the main memory, so they are the best at handling temporarily data.
```
### `=> Interrupts:`
```
 Interrupts are signals sent to the CPU by a hardware device (e.g to read from the keyboard), or a software program (int 0x10), they allow the CPU to temporarily stop whatever it's doing now and start the higher-priority task before returning to lower-priority task and finish it also.
 Each interrupt is represented by an index in the interrupt vectpr, that contains pointers to the INTERRUPT SERVICE ROUTINES (ISRs). it's also a sequence of machine instructions that deals with specific interrupts, For Exmaple interrupt 0x10 couses the screen-related ISR to be invoked, while 0x13 invok the disk related I/O ISR.
```
### `=> Memory ... For Now:`
```
 So We want to use all of the BIOS mechanics to be able to print/read stuff and switch to 32 bit mode, if you thought that it's will be for free you're wrong. check this memory layout so you can understand better.	
          ______________________________________________
          |                                            |
          |                FREE MEMORY                 |
0x100000  |____________________________________________|
          |                BIOS(256 KB)                |
0xC0000   |____________________________________________|
          |            Video Memory(128 KB)            |
0xA0000   |____________________________________________|
          |       Extended BIOS Data Area(639 KB)      |
0x9fc00   |____________________________________________|
          |                FREE (638 KB)               |
          |                                            |
0x7c00    |       *Loaded Boot Sector (512 byte)*      |
0x500     |____________________________________________|
          |           BIOS Data Area (256 byte)        |
0x400     |____________________________________________|
          |       Interrupt Vector Table (1 KB)        |
0x0       |____________________________________________|

 Well the BIOS is loader to the memory with all it's utilities and code, then the BIOS loads our code into address 0x7c00 so it's will be sure not to mess-up any data before, this will mean that our data will be offsetted with 0x7c00, accessing any data will be like:
```
``` ASM
	mov al, [data + 0x7c00] ; data should point to 'H', like C/C++ for example, but because of the offsetting we need to add 0x7c00 to the address then dereference it.
	mov ah, 0x0e	; indecate the teletype output routine
	int 0x10		; invoke the ISR

	data:			; data will be the address of 'H'
		db 'Hello', 0
```
```
 Of course we can make our life easier by setting the org to 0x7c00 so it will automaticely add it to our addresses instead of us typing it each time:
 	[org 0x7c00]
```
### `=> The Stack:`
```
 The stack is a continues memory, that offers the solution to the limited amount of registers CPU have to hold temperory data, The stack offers two instructions PUSH and POP. Push allow us to store data into the top of stack, and pop allow us to retrieve the data at the top of the stack.

 The stack can be implemented in different ways, in our case it's implemented by two Special purpose registers BP (Base Pointer) and SP (Stack Pointer), we set the base pointer to somewhere far away from other codes (BIOS code for example), to not overwrite any important data when expanding the stack. When Pushing data into the stack we increment SP to point into it, and pop get us the data pointed to by SP and than decrement it.
```
### `=> If ? If If If ? For Ifs:`
```
 Talking about coding an variables will make you wonder how can you use your everyday control structures in Assembly (if, else, for ...), Assembly offers instructions that will help you achieve that, and I'll give examples with the C code and it's alternative in Assembly.
```
### `If Else`
``` C
 int ax = 50;
 int bx = 10;
 if (ax > bx)
	printf("%c", 'Y');
 else
	printf("%c", 'N');
```

``` ASM
 mov ax, 50	; store 50 into ax
 mov bx, 10
 cmp ax, bx ; compare the two values
 jg  yes	; if ax greater then bx jump to label yes
 jmp no		; else jump to label no
 
 yes:
 	mov al, 'Y'
 	jmp	print	; jmp to label print
 no:
 	mov al, 'N'
 print:			; print what ever in al
 	mov ah, 0x0e
 	int 0x10
 end:
	jmp end	; infinite loop
; our magic number and padding
times 510 -( $ - $$ ) db 0	
dw 0xaa55
```
### `Add A Loop:`
``` C
 int cx = 5;
 int bx = 10;
 while (cx != 20){
 if (cx > bx)
	printf("%c", 'Y');
 else
	printf("%c", 'N');
 printf("\n");
 cx++;
}
```

``` ASM
mov cx, 5
mov bx, 10

looop:
	cmp cx, 20
	je  end		; jump to end if cx equal 20
	cmp cx, bx
	jg  yes
	jmp no

yes:
	mov al, 'Y'
	jmp	print
no:
	mov al, 'N'	
print:
	mov ah, 0x0e
	int 0x10
	mov al, 10	; 10 is new line in ascii
	int 0x10
	inc cx		; increment cx
	jmp looop	; continue the loop
end:
	jmp end
; our magic number and padding
times 510 -( $ - $$ ) db 0	
dw 0xaa55
```