---
title: "BootingSequence"
date: 2023-01-31T18:30:36+01:00
draft: false
tags: ["OsDev"]
---

``` C
	Welcome from now on, we will start the real work.
```

# Starting from the beginning:  
### `=> Booting your computer:`
```
	Booting is a startup sequence that starts the Operating system in your computer once it's turned on. The boot sequence is the initial set of steps/instructions that your computer perform when it's swiched on.
```
###	`=> CPU: `
```
	CPU (The Central Proccecing Unit) is the brain of our computer, it's the piece that handles all the instructions and operations you'll need to open youtube and watch some cat videos (The GPU plays a role when it comes to media ...), But well this means that the cpu is useless without instructions in at the first place ? Well welcome our friend the boot loader.
```
###	`=> Boot Loader: `
```
	CPUS can only execute the code in the system memory of course, Systems store code and data that will be executed ones the computer turned on in a non-volatile memories or storage devices, at the start of the computer it will execute the code in the nonvolatile portions of the system memory, than at boot time it will look for boot loader which is a 512 byte that ends with a magic number 0xAA55, once it finds it it will start executing it to load our Operating system.
```

###	`=> Boot Devices: `
```
	A Boot Device is from which the operating system is loaded, the BIOS(Basic Input/Output System) supports booting from various types of devices (USB drives, CD Drive, Hard Disk Drive).
```
### `=> Boot Sequence:`
```
 The standard boot sequence consist of:
  - The CPU runs the instruction in memory for the BIOS, instructions of jumping and loading to transfers to tbe bios start-up program.
  
  - The BIOS start-up program runs a power-on self test (POST) to check that all devices work properly.
  
  - The BIOS goes through the boot sequence until it finds a bootable device.
  
  - The BIOS loads the boot sector (our 512 byte with the magic number), and transfer execution to the boot sector.
  
  - From now on Our code is the one executed and we have full control over the system.

```