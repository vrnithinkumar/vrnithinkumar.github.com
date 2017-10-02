---
layout:     post
title: Assembler & Linker
date: 2016-12-04 00:10:03
subtitle: ""
author:     "Nithin VR"
header-img: "Assembly.jpg"
catalog: true
tags:
  - Compiler
  - Assembler
  - Linker
---
## Introduction

Between compiling and executing a High-Level programming language, Many intermediate steps like Lexical Analyzing, Syntax Analyzing, Semantic Analyzing, Pre Optimizing, Assembly Code generation, Post Optimizing,  Assembling,  Linking and Loading are happening. In this post we are mainly covering 
  - Assembler
  - Linker
  - Loader
  - Disassembler

### Assembler

Assembler will create  object code from the assembly code. After Assembly Code generation and Post Optimizing, the generated assembly code is translated to Object code. Assembly Language aka **asm** is low-level programming language which is specific for CPU architecture. Its actually human readable representation of machine code.
For example:
Assembly Code for moving value 55 hex into register BL

    MOV BL, 055H

Corresponding Machine Code

    10111000

An assembler will generate object code by translating combinations of mnemonics and syntax for operations and addressing modes into their numerical equivalents. Each object code file combined to create the executable file.

**In short, assembler will** 
 - Translate assembly instructions, macros, and  pseudo-instructions into binary machine instructions
 - Convert decimal numbers, etc. specified by programmer into binary.
 - Put the translated instructions into a file for future use.

**Types of Assemblers**

** 1) Two pass assembler **
In two pass assembler we use two pass to complete generation of machine code. First pass we will create a symbol table **(SYMTAB)** where we keep all the symbols and their values. Any unknown values or addressed we encounter we will add it to the symbol table and update it when we encounter that value in future. For example 

    10 JMP LOOP_END 	\\ Address of label2 is unknown we add this to symbol table.
    11 ADD  EAX EBX
    12 LOOP_END:       \\ Here we update the symbol table with actual address.
    
    Symbol Table 

    | Symbol Name | Symbol Value |
    |:-----------:|:------------:|
    |    LOOP_END |      12      |
    |    Label2   |      18      |

Using the symbol table we will create valid machine code on the second pass.

**2) One pass assembler**
In one pass assembler, we convert the whole assembly code in to machine code by one pass unlike two pass assembler. We keep different type of symbol table where each unknown symbols and all locations it used are stored. Whenever we encounter the initialization of the symbol we will check the symbol table and go back to  update all places it used with the actual value. 

**Object File** 

Object files are group of blocks of information about the program in binary format. Blocks contain program code,  program data, and data structures that guide the linker and loader. Its the output from the assembler there are different forms of object file based on how it used.: 
 1. **Relocatable object file**. Contains binary code and data in a form that can be combined with other relocatable object files at compile time to create an executable object file.
 2. **Executable object file**. Contains binary code and data in a form that can be copied directly into memory and executed.
 3. **Shared object file**. A special type of relocatable object file that can be loaded into memory and linked dynamically, at either load time or run time.  

Common object file formats
 - **COFF :** Common Object File format. Windows uses a variant of COFF called the Portable Executable (PE) format.
 - **ELF :** Executable and Linkable Format Mainly used in Linux.

### Linker
Linker will take all the object file created by the assembler and combine them into a single executable file , library or another big object file.Which can be be loaded to the memory and execute. It enable separate compilation and gives modularity to big programs.

- **Symbol resolution.** 
Object files define and reference symbols. The purpose of symbol resolution is to associate each symbol reference with exactly one symbol definition.

- **Relocation.** 
Compilers and assemblers generate code and data sections that start at address 0. The linker relocates these sections by associating a memory location with each symbol definition, and then modifying all of the references to those symbols so that they point to this memory location.

- **Static linkers.**
Such as the Unix ld program take as input a collection of relocatable object files and command line arguments and generate as output a fully linked executable object file that can be loaded and run.