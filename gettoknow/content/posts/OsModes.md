---
title: "OsModes"
date: 2023-02-01T18:45:02+01:00
draft: false
tags: ["OsDev", "BIOS"]
---

``` C
	Real mode ? Imaginary mode ?
```

# Real / Protected / long Modes:

### `=> What are these anyway:`
```
	Real mode, protected mode, and long mode are different operating modes that have been introduced in x86 architecture-based computers to provide various levels of memory access and protection, as well as other features and capabilities.
```

### `=> So Real Mode is not ?:`
```
	Real Mode is a 16-bit mode that was used in the early x86 processors, and it's the mode that the computer uses ones it turns on, so even our friend the BIOS, functions in 16-bit mode. It provides limited memory access (1 MB of memory) and used for compatibility with old software. Real Mode doesn't offer any complicated state, no memory protection or multitasking capabilities.
```
### `=> Protected Mode will fix it ... right ?:`
```
	Protected mode is a 32-bit mode that provides memory protection, virtual memory, and multitasking capabilities. Protected mode allow the CPU to operate in a more advanced state, it uses segmentation to devide memory into different segments, each with it's own access rights, allowing better control over memory access.
```
### `=> Protected mode Did it ... Why long mode ?:`
```
	Long mode is a 64-bit mode available. It provides 64-bit addressing and supports up to 2^64 bytes of memory. Long mode also includes a compatibility mode with 32-bit protected mode, allowing 32-bit operating systems and applications to run on a 64-bit processor. Long mode provides the processor with the ability to access large amounts of memory, making it possible to run applications that require a large amount of memory.
```
### `=> So at the end who won ?:`
```
	I bilieve that 32-bit more is more than enough for our Hobby-operating system, it may change in the future but for now I'm going to focus on understanding and building this in protected mode.
```