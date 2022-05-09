# Solution for soDifficult

Firstly, we extract the apk, convert the apk to jar file using "dex2jar" , and view the decompiled jar using "jd-gui" tool.

A quick look at com.ctf.rev/Main Activity.class shows that it compares the password we entered using a function checkPassword3() implemented
natively in the "native-lib" library imported in this class. 

![image](https://user-images.githubusercontent.com/73381089/167371145-a59220da-4c42-43ca-8454-c80f95f635c2.png)

This library file can be found at: `lib/x86_64/libnative-lib.so`. Let's open this file and examine the checkPassword3(char *s) function in ghidra:

![image](https://user-images.githubusercontent.com/73381089/167373874-c4b87fcb-f42f-4190-acc7-c253b6214ad3.png)

We can see that this function has a lot of equations like:
```
s[14] * s[11] + s[14] + s[3] = 0x2cbb
s[6] * 2 - s[11] = 0x7d
s[7] * s[11] = 0x1a59 
```
and many more, all of which need to be satisfied for the function to return true. 

Now, we can use any equation solver like z3 to obtain the solution, or solve it by hand like I did to get the flag: ctf{k1nG_0f_R3v3r5e}
