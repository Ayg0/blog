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

 Well the BIOS is loader to the memory with all it's utilities and code, than the BIOS loads our code into address 0x7c00 so it's will be sure not to mess-up any data before, this will mean that our data will be offsetted with 0x7c00
```
