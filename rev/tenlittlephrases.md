# Solution for TenLittlePhrases

Firstly, I downloaded the binary and loaded it into Ghidra.

A quick look shows there are 4 relevant functions: main(), getInput(), printFlag(), decryptAndPrint()

And voila, printFlag() and decryptAndPrint() functions are exactly same as before. 
So we can just repeat the same steps we can use for ExcessChars challenge. 

Note to admins: Please host pwn challenges on a server next time instead of directly giving us the binaries :)
