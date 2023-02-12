---
title: "BootSector"
date: 2023-02-01T18:41:24+01:00
draft: false
tags: ["OsDev"]
---

``` C
	are you my 512 byte ? nah nah where are you my  0xAA55 ?
```

# BIOS And The Boot Sector:  
### `=> The Proccess:`
```
	You've learned in previous chapters, The one taking control over the computer at the start up is the BIOS, after it's checks (POST) it will try to load an operating system, It's will look at the first 512 bye of each drive, the 512 byte of data called a sector, it's will be bootable if it finds at the end of that 512 byte the bytes 0x55 0xAA, which are the magic number for a boot sector, than it will load that 512 byte and hand the control to it starting to execute the instructions inside, here where we should put the code to load more memory, change the mode from real mode to protected mode and so on.
```
### `=> The Bare Minimum:`
```
	let's start with the bare minimum to have a bootable device:
		- 512 byte;
		- end with the magic number 0x55AA;
	so when we view our boot.bin whith hexdump for example, should look something like this:
	
	 e9 fd ff 00 00 00 00 00 00 00 00 00 00 00 00 00
	 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
	 *
	 00 00 00 00 00 00 00 00 00 00 00 00 00 00 55 aa
	
	for that we may write it ourselves (the dump way) or we can embrace the power of assembly and start coding.
```
### `=> Boot.asm:`
``` asm
	loop:			
		jmp loop ; creating an infinite loop so the cpu will not execute something we didn't wrote

	times 510-($-$$) db 0 ; sets a block of memory with the value 0
						  ; $ is the current location counter (the address of the next instruction to be executed).
						  ; $$ is the location counter for the beginning of the current section ( beginning of the sector).
						  ; substructing ($ - $$) from 510 will give us how many byte we still need to fill with 0 to have 510 byte of memory.
	dw 0xaa55			  ; adding 0xaa55 will be the magic number and add 2 more byte so we have a full 512 byte of memory that ends with it.
```
### `=> Building And Testing:`
```
	To test if our little code works we will need QEMU and NASM:
	
 Let's start with compiling our code using:
	$> nasm -f bin boot.asm -o boot.bin

 And run it using:
	$> qemu-system-x86_64 boot.bin

 If we did everything correctly QEMU should output this:
```
![QEMU WORKING](/gettoknow.github.io/s_BS.png)