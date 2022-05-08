# Solution for GetArgs

Firstly, I downloaded the binary and loaded it into Ghidra.

![Screenshot_20220508_235107](https://user-images.githubusercontent.com/73381089/167310021-90617a84-f9dd-4dd7-bb2a-74cc8791af66.png)

We notice that each character of our input (argv[1]) is compared against the corresponding character of another fixed string "AJi9VL0C4p",
and should have a difference equal to variable iVar2, value of which is determined using another function => QM9Nq(). 

A quick look in QM9Nq() shows that it always produces a constant value. Instead of reversing the logic, I simply created a new C binary,
with code copy pasted from that function, and ran it to find the value it returns, which is 3.

Now we can just use python, to obtain the accepted input:

```
s = "AJi9VL0C4p"
for c in s:
  print(chr(ord(c) - 3), end="")
```

With this, we get the output: >Gf6SI-@1m <br />
Hence, our flag is: CTF{>Gf6SI-@1m}
