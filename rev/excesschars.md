# Solution for ExcessChars

Firstly, I downloaded the binary and loaded it into Ghidra.

A quick look shows there are 4 relevant functions: main(), getInput(), printFlag(), decryptAndPrint()

The main() function gets input from user using getInput() and calls a dummy function putchar(10) (for whatever reason) and exits.
The printFlag() function checks if we are using a debugger, and if not, calls decryptAndPrint() which prints the obfuscated flag.
The getInput() function gets input from the user and is susceptible to buffer overflow attacks.

Seems like the problem setters wants us to do ROP from getInput() to printFlag(), since we obviously can't directly just run that function
from a debugger... or can we? (insert vsauce theme)

Well, just directly running the following commands in gdb:
```
start
set $eip = printFlag
continue
```
won't work as the printFlag() checks if we are using a debugger or not. We can try to directly run the decryptAndPrint() function through gdb,
but it prints a gibberish flag for some reason. 

But, we can just patch the binary and make the if(condition) in printFlag() always true. And then directly run the printFlag() function.

For this, we load the binary in radare2 in write mode, analyze it (aaa, afl), seek to printFlag() (s sym.printFlag()), and enter visual disassembly mode. 
Then, press "c", seek to the jns instruction (conditional jump), press "i", enter "eb" (opcode for simple jmp instruction), press enter, and quit.
Now, we have patched the binary such that it checks if we are using a debugger in printFlag(), but jumps to decryptAndPrint() anyways.

![Screenshot_20220509_004102](https://user-images.githubusercontent.com/73381089/167311934-620d6190-93cf-4bb5-bb6a-9d224c0457b4.png)

Now, we load the binary in gdb, and run the above mentioned gdb commands and get our flag: CTF{3xpl0!t_Rev3rs!nG_3G}
