# Shellcoder's Handbook work

The stuff I put together (examples, experimentation) while working through the [Shellcoder's Handbook](https://www.amazon.com/Shellcoders-Handbook-Discovering-Exploiting-Security/dp/047008023X/ref=sr_1_1) (Note: while the link is for amazon, I actually got the book via the Cybersecurity 2020 bundle on Humble Bundle for $1.50 :D )

My dev machine for this stuff was primarily WSL2 on Windows 10 running an Ubuntu dist (18.04.4 LTS), for those interested. And as an editor I used VS Code - interestingly, I used VS Code from Windows, with a terminal window using bash in the same folder to compile projects, use GDB etc. What a world we live in xD

## Building 32-bit exploitable programs on WSL2

WSL for windows is 64 bit only, meaning by default you won't be able to run, and potentially not even compile 32 bit programs. The code in the book is largely 32 bit (so far), so this presents a small problem.

I could compile to 64 and see where things are at, but I wouldn't mind getting familiar with 32 properly first.

Fortunately, while WSL1 was sort of 'emulated linux', WSL2 is actual linux running in a VM system. Meaning that I should be able to get it to run 32 bit and even turn off all the protections like ASLR that make the samples not work.

### QEMU for 32 bit

[QEMU](https://en.wikipedia.org/wiki/QEMU) is, ironically, basically to linux what WSL1 was to windows: an emulator. But it allows me to run and even debug 32 bit executables, so its fine. The performance doesn't matter for this book. The following instructions were sourced from [this issue comment](https://github.com/microsoft/wsl/issues/2468#issuecomment-374904520):

1. Install QEMU with the following commands:

```bash
sudo apt update
sudo apt install qemu-user-static
sudo update-binfmts --install i386 /usr/bin/qemu-i386-static --magic '\x7fELF\x01\x01\x01\x03\x00\x00\x00\x00\x00\x00\x00\x00\x03\x00\x03\x00\x01\x00\x00\x00' --mask '\xff\xff\xff\xff\xff\xff\xff\xfc\xff\xff\xff\xff\xff\xff\xff\xff\xf8\xff\xff\xff\xff\xff\xff\xff'
```

2. Start it with the following command: `sudo service binfmt-support start`

3. Enable i386 architecture and packages:

```bash
sudo dpkg --add-architecture i386
sudo apt update
sudo apt install gcc:i386
```

Thats it.

## Compiling to exploit

To compile 32 bit programs so that the samples work properly, or at least so I don't need to do them on hard difficulty, I need to turn off all the protections add to prevent precisely the stuff the book talks about.

These are:

- stack protection, which prevents 'stack smashing'
- position independent code
- address space layout protection

And I need to compile 32 bit rather than the default 64 bit.

Most of the above can be done on the command line with gcc/cc, but first to turn off ASLR you need to modify a file in your distro / WSL instance: `echo 0 | sudo tee /proc/sys/kernel/randomize_va_space`

Once thats done, to compile the samples I typically used a command like:

```bash
cc -mpreferred-stack-boundary=2 -fno-stack-protector -fno-pie -ggdb -m32 code-to-compile.c
```