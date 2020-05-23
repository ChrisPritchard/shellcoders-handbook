# Buffer Overflows

A set of notes, largely to myself, about how buffer overflows work.

1. First point to note is that programs have an instruction pointer that points to the next **address** to be executed.

2. When a function is 'called' in assembly, even main (which is invoked by entry I believe), the first thing that happens is that the instruction pointer is pushed onto the stack. This is so when the function 'returns', the pushed instruction can be popped back into the current instruction pointer, effectively setting the next instruction to be what it was before the function was called. Accordingly, if you breakpoint a call instruction in assembly and make a note of the address of the instruction following, when you proceed into the called function, you should see that address appear in the stack.

3. The stack is a block of space, used for many things. Importantly, when a function declares variables a certain amount of space is cleared on the stack. This 'space reservation' is by moving the stack pointer down a certain amount: when things are pushed onto the stack, or written onto the stack, it grows up (in some architectures this is reversed: the stack pointer is increased and grows down).

4. Some functions like `gets` and `strcpy` place a string into a buffer, or array. These functions specifically don't check that the buffer is the right size for the string going into it. As buffers and other variables are just predefined stack space, writing too much data flows over the assigned space and up (or down) the stack, overwriting other things.

5. If the overflowing data overwrites the saved intruction pointer, then when whatever function you are in ends, the code will attempt jump (via overriting the instruction pointer) which whatever value you have overflowed that specific position in the stack.

6. Normally this will result in a seg fault, which is when the code tries to jump somewhere its not allowed. For example, commonly you use long strings of AAAAAA to test overflows, which in hex is 0x41. If you debug a program (e.g. with GDB or Radare) and it seg faults with an error like "failed to jump to 0x41414141" this is a sign you have overwritten the return address with AAAA.

7. To create a controlled, typically shell creating overflow, you would stick shellcode somewhere (likely in your overflow payload, if the buffer is big enough), then try to overwrite that return address with the valid address of your payload. In this way, when the function ends it will jump to your code and execute it.

8. The problem with this is that guessing the exact address of your shellcode is hard. Stack addresses are not necessarily predictable, and getting even one byte off will result in an error. The solution, for older programs without some more modern protections, is to create what is called a nop-slide into your shell code, then pick an address that is likely to land in this nop-slide.

9. A nop slide is a long set of "no operation" instructions, which have the hex code \x90. When a processor runs one of these, it just continues. As such, a long set of nop instructions will result in, if the CPU starts running any of them, it 'sliding' to the end where you put your shell code.

10. So, a successful buffer overflow relies on the following steps:

- find the exact size of data you need to overflow the return address. This can be calculated if you know the source code, but usually its just easier to try random lengths and refine your way to the exact size: i.e. you might find a size 171 string succeeds fine, but a size 172 string corrupts the return address. This means (on a 32 bit program, 171 characters plus a 4 character return address is what you need to pass in).

- get some shell code. You can write it yourself (usually by compiling a very simple c program, extract its assembly, ensure there are no null bytes in it \x00 as these would terminate the buffer string, and then putting the result into a line of hex codes) but its far easier to just get some online.

- compile a string that is a bunch of nop instructions (maybe 100+ if you have the space) plus the shell code, plus whatever is left, plus a return address.
