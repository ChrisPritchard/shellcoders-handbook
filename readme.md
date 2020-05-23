# Shellcoder's Handbook work

The stuff I put together (examples, experimentation) while working through the [Shellcoder's Handbook](https://www.amazon.com/Shellcoders-Handbook-Discovering-Exploiting-Security/dp/047008023X/ref=sr_1_1) (Note: while the link is for amazon, I actually got the book via the Cybersecurity 2020 bundle on Humble Bundle for $1.50 :D )

I also conducted experiments with the original [Smashing the Stack article by Aleph One in Phrack magazine](http://phrack.org/issues/49/14.html#article) ([PDF version](http://www-inst.eecs.berkeley.edu/~cs161/fa08/papers/stack_smashing.pdf)).

Both the book and the article by Aleph One cover 32 bit machines. This is harder to do now, in 2020, especially since I did these challenges on machines running Windows 10. While I initially tried doing them under WSL this didn't work as WSL on Windows is 64 bit only. Ultimately, the solution was to use a 32bit Ubuntu virtual machine (I used the 16.04 LTS Server image) on Virtualbox. Note Virtualbox instead of Hyper-V: Linux seems to run better on Virtual Box, and also I believe Hyper-V likewise doesn't support 32 bit machines.
