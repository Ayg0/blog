---
title: "GccCrossCompiler"
date: 2023-01-27T19:08:36+01:00
draft: false
tags: ["Compiler", "OsDev"]
---

# Cross-Compilers
## > What's a Cross Compiler ?  
		- A cross Compiler is simply a compiler that runs on the host platform (for example MacOS), and generate executables for the target platform (for example Windows).
## > Why we need a Cross Compiler ?
		- The only reason not to use a cross compiler is if you're generating executables for your own machine, where both the host and the targets are the same, and it's not the case for our projects now.
## > What's out target platform:
	I may change this in the future but for now it's will be the i686-elf:
	
		- Architecture i686 indicates that the target machine uses 32-bit x86 architecture.
		- ELF (Executable and linkable Format) is a standard file format for executables, object code, and shared libraries.
		- most important part is that we've specified that the target machine does not have a complete operating system.
## > How to build one ?
```
 Building a cross compilers varies from an OS to an other, I'm going to build this GCC-CrossCompiler in MacOs even tho building in in linux maybe easier because of the dependancies That are pre-installed there, once we do that we need to download, configure and install binutils and gcc.
```
## > preparation :
```shell
	brew install gmp mpfr libmpc	# installing using brew
	export CC=$(PATH_TO_LOCAL_GCC)
	export LD=$(PATH_TO_LOCAL_GCC)
	export PREFIX="~/TOOLS/i386elfgcc"	# where you'll find the compiler, linker and other tools.
	export TARGET=i386-elf
	export PATH="$PREFIX/bin:$PATH"
```
## > binutils :
```shell
	mkdir /tmp/src
	cd /tmp/src
	curl -O http://ftp.gnu.org/gnu/binutils/binutils-2.24.tar.gz # there is more recent versions.
	tar xf binutils-2.24.tar.gz
	mkdir binutils-build
	cd binutils-build
	../binutils-2.24/configure --target=$TARGET --enable-interwork --enable-multilib --disable-nls --disable-werror --prefix=$PREFIX
	make all install
```
## > GCC :
```shell
	cd /tmp/src
	curl -O https://mirror.marwan.ma/gcc/releases/gcc-7.4.0/gcc-7.4.0.tar.gz # Morocco have a mirror :) :O
	tar xf gcc-7.4.0.tar.gz
	mkdir gcc-build
	cd gcc-build
	../gcc-7.4.0/configure --target=$TARGET --prefix="$PREFIX" --disable-nls --disable-libssp --enable-languages=c,c++ --without-headers --disable-werror --with-gmp=$(brew --prefix gmp) --with-mpfr=$(brew --prefix mpfr) --with-mpc=$(brew --prefix libmpc) # configured for both C and CPP
	make all-gcc 
	make all-target-libgcc 
	make install-gcc 
	make install-target-libgcc 
```
## > TIPS :
```
	- You'll find your desired tools in the $PREFIX location you specified before.
	- Consider adding the path to those tool in the PATH variable so you'll not need to specify the whole path everytime you want to call them.
```
