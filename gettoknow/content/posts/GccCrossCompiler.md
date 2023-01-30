---
title: "GccCrossCompiler"
date: 2023-01-27T19:08:36+01:00
draft: false
tags: ["Compiler", OsDev]
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
		- Building a cross compilers varies from an OS to an other, I'm going to build this GCC-CrossCompiler in MacOs even tho building in in linux maybe easier because of the dependancies That are pre-installed there.

		-> Step 1:
			- Installing HomeBrew which will be the package manager we will use to install all the necessary tools.

		-> Step 2:
			- Installing dependancies simply use:
				$> brew install binutils bison flex gmp mpfr libmpc isl

		-> Step 3:
			- Downloding and extracting the GCC source code:
				$> git clone git://gcc.gnu.org/git/gcc.git /Example/Disired/Working/Directory/gcc

		-> Step 4:
			- Configuring GCC source code:
				$> ./configure --prefix=/WhereToInstall/i686-elf --target=i686-elf --disable-nls --enable-languages=c,c++ --without-headers --with-gmp=$(brew --prefix gmp) --with-mpfr=$(brew --prefix mpfr) --with-mpc=$(brew --prefix libmpc)

		-> Step 5:
			- build and install the cross-compiler:
				$> make all-gcc ; make install-gcc

		- If everything went correctly you should be able to use "i686-elf-gcc -v" command.
