---
title: "BIOS"
date: 2023-02-01T18:44:54+01:00
draft: false
tags: ["OsDev", "BIOS"]
---

``` C
	BIOS: you need int 0x10 not int 0x13
```

# The BIOS:

### `=> What's the BIOS:`
```
	BIOS (Basic Input/Output System) is a type of *firmware that provides an interface between computer's hardware and software (OS), it's stored on a *non-volatile memory chip on the mother board.
	The BIOS is the First program loaded When the computer is powered on, and it's responsible for performing a series of system checks and initializations.
```

### `=> BIOS ? Why bother ?:`
```
	The BIOS and the understanding of it, plays a big role in the early stages of the Os Dev journey. Some of the most important tasks the BIOS perform are:
		- POST: The BIOS perform a series of checks to verify that the hardware is working correctly.
		
		- Boot Sequence: The BIOS determines the boot device and load the boot loader into memory, and passes control to it.
		
		- Configuration: The BIOS allow us to change some options such as the date/time, the boot sequence and memory settings.
		
		- Input/Output Services: The BIOS provides basic input/output services (Reading from the keyboard and printing to the screen).
```

### `=> BIOS ? What's about UEFI:`
```
	UEFI (United Extensible Firmware Interface), has replaced the BIOS in newer computers, because it provides a more advanced interface for configuring the system (also you can use your mouse), still BIOS is all what we need no fancy stuff and well I don't think that I'll need a modern computer to run my OS anyway.
```