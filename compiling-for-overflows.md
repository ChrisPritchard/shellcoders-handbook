# Compiling for Overflows

Modern operating systems and compilers make a lot of effort to prevent 'trivial' buffer overflows. I say trivial, because buffer overflow vulnerabilities still keep happening - its really an arms race between attacker techniques, operating system and compiler protections, and the developers in the middle who keep both in business.

Anyway, this repo and a lot of CTFs don't tend to ask for the elite of the elite of overflow attacking: instead they rely on a lot of these protections being disabled. A nice overflowable program, like those from the original aleph-one 'smashing the stack for fun and profit' (a copy of which is in this repo), has a predictable stack space and an executable stack. In order to build such today, you have to do a few things:

## Disable ASLR

This is a feature that makes the addresses used when your decompiling randomised. E.g., in contrast to what Aleph One says in his article, the stack does *not* always start at the same address, and will be different each time. This makes guessing the address to overflow the ret address with much more difficult. To disable this temporarily for your current book session, use:

`echo 0 > /proc/sys/kernel/randomize_va_space`

## Compile with gcc protections off

GCC will compile C code with additional protections, like guards against the stack being executable (meaning you can't stick shell code in a buffer) and a 'canary' that will seg fault the application if overwritten, placed extremely inconveniently between the space allotted for variables and the return address. When compiling, use an instruction like the below to turn these off:

`gcc -fno-stack-protector -z execstack -o program.out program.c`
