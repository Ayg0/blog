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
 Interrupts are signals sent to the CPU by a hardware device (the keyboard), or a software program (int 0x10), they allow the CPU to temporarily stop whatever it's doing now and start the higher-priority task before returning to lower-priority task and finish it also.
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
0x7c00 ---|-->    *Loaded Boot Sector (512 byte)*      |
0x500     |____________________________________________|
          |           BIOS Data Area (256 byte)        |
0x400     |____________________________________________|
          |       Interrupt Vector Table (1 KB)        |
0x0       |____________________________________________|

 Well the BIOS is loaded to the memory with all it's utilities and code, then the BIOS loads our code into address 0x7c00 so it's will be sure not to mess-up any data before, this will mean that our data will be offsetted with 0x7c00, accessing any data will be like:
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
 The stack is a continues memory, that offers the solution to the limited amount of registers CPU have to hold temperory data, The stack offers two main instructions PUSH and POP. Push allow us to store data into the top of stack, and pop allow us to retrieve the data at the top of the stack.

 The stack can be implemented in different ways, in our case it's implemented by two Special purpose registers BP (Base Pointer) and SP (Stack Pointer), we set the Both of them to somewhere far away from other codes (BIOS code for example), to not overwrite any important data when expanding the stack. So by pushing data into the stack we actually put it into memory and increment SP, and by poping data we get it from the stack we decrement SP.
```
### `=> If ? If If If ? For Ifs:`
```
 Talking about coding and variables will make you wonder how can you use your everyday control structures in Assembly (if, else, for ...), Assembly offers instructions that will help you achieve that, and I'll give some examples with  C code and it's alternative in Assembly.
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
```
### `Functions ?:`
``` C
wow(){
	printf("W");
}

int	cx = 5;
int	bx = 2;
if (cx > bx)
	wow();

```

``` ASM
 [org 0x7c00]
 mov cx, 5
 mov bx, 2
 cmp cx, bx
 jl  end
 call wow ; calling a function allow us to return and finish from the next instruction.
 end:
	jmp end	; infinite loop
 wow:
	mov al, 'W'
	mov ah, 0x0e
	int 0x10
	ret			; returning will restore the values of the registers and also return to where we called the function to procceed to the rest of the code.
```
### `Orginize to Analize:`
```
 We've learned a lot so far and we will need to device our code into different files so we can make it more readable and reach each section.
 For that we can include files, by that you tell the compiler to replace the include "File.asm" line with what's inside the file. As an example we can put our Wow function into it's own file wow.asm:
```
``` ASM
 [org 0x7c00]
 mov cx, 5
 mov bx, 2
 cmp cx, bx
 jl  end
 call wow
 end:
	jmp end
 %include "wow.asm"
```
### `Have a tool and prepare:`
```
 our code will get just bigger and bigger, and with that bugs will appear and we have no tool to work against them, we will deal with values and memory addresses, and to check if everything is working correcly we would like to read those values, and here where printHex routine will help, it takes the value as an argument in ax, and display it into the screen. You can find it inside print.asm with the print_str routine with explanation.
```
### `0xffff too low ? Segmentation is the hero :`
```
 Well you've made it now you can print and debug all memory addresses you want ... except max you can get is 0xffff(2^16), which is too low for an operating system to work with, but with the help of segmentation we can get even more and finally achieve the 1Mb limit.
 Segmentation is implemented with different registers inside the x86 architecture which are cs (code segment),
 ds (data segment), ss (stack segment) and es (extra segment it's used to access more data).
 To demenstrate how this works, we can use our problem of the 0x7c00 offsetting, to handle it using segmentation we need to load the ds register with 0x7c0 so next time our program will be faced with something like:

	let's say:      'mov ax, [msg]'

	it's will do:   'mov ax, [0x7c0 * 16 + msg]' 

 the program will use ds by default unless you explecitly use an other register such as ss for example:

	'mov ax, [ss:msg]'

  
  Now you can now why we can reach 1Mb now (0xffff * 16 + 0xffff). When we switch to 32 bit mode we will use it to access even more memory.
```
### `I have the power ... still 512 byte:`
```
 well we have our 1Mb to play with, let's get more sectors loaded so we can have our operating system ready for 32 bit mode, When our BIOS loads the boot sector it stores the drive id into the dl register, storing it in a variable will be helpful when we want to read more from that drive, also thanks to the BIOS we don't need to worry about what type of storage we're using (USB, Hard Drive, Floppy Disk ...), using the BIOS disk routines (0x13) will put a layer of abstraction that will be handy in the future.
 We used the BIOS interrupt 0x10 to write before, now we will use the 0x13, like when we set up things like the character to print and the color, Here also we need to specify some details:
```
``` ASM
	mov [Disk_id], dl
	;[...] some code
	mov ah, 0x02         ; BIOS read sector function
	mov dl, [Disk_id]    ; read drive [Disk_id]
	mov al, [num_sector] ; number of sectors to read
	mov ch, 0x00         ; select cylinder 0
	mov dh, 0x00         ; select head 0
	mov cl, 0x02         ; start reading from the 2nd sector (first one already loaded by the bios).
	mov bx, 0x9000       ; where to put the data we've got
	int 0x13             ; BIOS interrupt
	jc Reading_error     ; if an error accured jump to the error routine.
	cmp al, [num_sector] ; compare to see if we loaded all the sectors we needed.
	jne Reading_error    ; jump to the error routine if they are not equale		
```
```
 We may run into errors while loading because of exceeding the boundries or maybe some genius removed the floppy disk, so we need to know when it happens.
 It's worth noting that the Cylinder-Head-Sector (CHS) addressing is used.
```


