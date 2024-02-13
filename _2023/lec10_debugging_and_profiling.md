---
layout: lecture
title: "Debugging and Profiling with gdb"
date: 2024-02-13
ready: false
phase: 2
---

gdb - GNU Debugger



useful in base form but better with extensions

one of these is peda

can be downloaded from https://github.com/longld/peda

using:

git clone https://github.com/longld/peda.git ~/peda
echo "source ~/peda/peda.py" >> ~/.gdbinit
echo "DONE! debug your program with gdb and enjoy"

however this saves the peda directory to your home dir, can be edited differently



to start run gdb and you should see the prompt: `gdb-peda$`

gdb has clever autocomplete: for example typing `b` or `br` instead of `break` or `i r` instead of `info registers`


to open an executable `file [FILE NAME]`

`checksec` - checks security measures on the executable

to list decompiled code lines fromthe point of exection use `list .` and repeat `list` to read further

`list .` has to be repeated to restart reading code from the current location

`list -` and `list +` can be used to list lines before and after the current location respectively

`disas main` - disassemble the main function and list in assembly code

to run it use `run`


if you want to stop a program at a specific place to debug it you'll have to use breakpoints

these stop the program's execution at specific points and allow the executable to be edited and inspected at certain states


`break main` sets a breakpoint at the start of the main function

executing `run` after this will stop the program just before the first instruction of the main function and pull up a context menu

the context menu can be reviewed with `context` and displays registers, assembly code, and the stack, as well the data that pointers are pointing to



breakpoints can be set to lines to code with `break [LINE NUMBER]`

to set breakpoints in specific memory locations, you can use `disas main` to list the main function, or `disas [FUNCTION NAME]` for another specific function to find the line at which you'd like to set a breakpoint

then run `break *[FUNCTION NAME] + [LINE]`, for example `break *main + 4` to break when the 20th memory value of the main function is reached

the `*` operator dereferences references to areas of memory, somewhat similarly to pointer dereferencing in code, for example *main would point to the area of memory where the main function starts

peda gdb will dereference some functions for you, but it's useful to know



the contents at a memory address  can also be read with the `x` command with the following syntax:

`x/[LENGTH][FORMAT] [MEMORY ADDRESS]

where [LENGTH] and [FORMAT] are optional and the `/` operator is only used when either of them are provided


| Character | x           | i           | s      | a       | d       | u                | c    | f              | t      | o     |
|-----------|-------------|-------------|--------|---------|---------|------------------|------|----------------|--------|-------|
| Format    | Hexadecimal | Instruction | String | Address | Decimal | Unsigned Decimal | Char | Floating Point | Binary | Octal |


examples:

`x main` reads the first memory value in the main function

`x/x $rip` reads the value of $rip (the next instruction) as hex

`x/20 $rsp` reads the first 20 memory values in the stack (pointed to by $rsp)

`x/20i printf` `reads the first 20 values in the print function as instructions



registers can be set with `set $[REGISTER] = [VALUE]`, but this can easily break the program

registers can be read with `info reg`

functions can be read with `info func`

frame data can be read with `info frame`

local variables can be read with `info local`




`backtrace` can be used to read the call stack order

`backtrace full` does the same but displays extra information like symbol tables
