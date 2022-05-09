# Solution for Network

Firstly, we extract the apk, convert the apk to jar file using "dex2jar" , and view the decompiled jar using "jd-gui" tool.

A quick look at com/example.hellojni/Main Activity2.class shows that app tries to send HTML requests to "google.com/flag" which should fail, since 
that link doesn't exist. The class also imports a native library "hello-jni". 

![Screenshot_20220509_132107](https://user-images.githubusercontent.com/73381089/167365121-6fabf572-ac06-4209-aa72-f1871b4ba454.png)

Let's take a look at the imported library. The library file is located in lib folder. I used the x86_64 one: `lib/x86_64/libhello-jni.so`.

I loaded the library in ghidra and found some interesting functions: getKey(), getURL() and getPath(). <br/>
Let's examine the getPath() function. It seems to have a path string which is encrypted with AES-128-ECB algorithm with PKCS5Padding.

![image](https://user-images.githubusercontent.com/73381089/167366213-04692476-b1fd-4cc8-8b9b-ea62a4ff34e6.png)

The key for encryption is obtained via the getKey() function. Since the apk is named malware, I assumed that the app is somehow sending HTML requests
to this path in the background, through some part of code I haven't explored yet. Let's try to decrypt this "path".

We need to first obtain the key from getKey() function. That function seems to just base64 decode a string stored on stack, and return it.

![image](https://user-images.githubusercontent.com/73381089/167366410-6b8cb386-24b2-4487-bcc2-159a563f42e4.png)

The encoded string is: "MTIzNDU2Nzg5MGFiY2RlZg" which decodes to "1234567890abcdef". Now that we have obtained the key, we can decrypt the path.

The encrypted (and base64 encoded) string is: "tmhnj20r1KJPexqEWFEkx4taM6wVJh5wK9YHkB0mAzlzisYIoO4qP_2OCpHOWr". Obtaining this string from ghidra is
a bit painful, so I just used gdb to obtain it:

```
(gdb) disas getPath
Dump of assembler code for function getPath:
   0x0000000000001530 <+0>:     push   %rbp
   0x0000000000001531 <+1>:     mov    %rsp,%rbp
   0x0000000000001534 <+4>:     sub    $0xa0,%rsp
   0x000000000000153b <+11>:    mov    %fs:0x28,%rax
   0x0000000000001544 <+20>:    mov    %rax,-0x8(%rbp)
   0x0000000000001548 <+24>:    movaps 0x2801(%rip),%xmm0        # 0x3d50
   0x000000000000154f <+31>:    movaps %xmm0,-0x20(%rbp)
   0x0000000000001553 <+35>:    movaps 0x27e6(%rip),%xmm0        # 0x3d40
   0x000000000000155a <+42>:    movaps %xmm0,-0x30(%rbp)
   0x000000000000155e <+46>:    movaps 0x27cb(%rip),%xmm0        # 0x3d30
   0x0000000000001565 <+53>:    movaps %xmm0,-0x40(%rbp)
   0x0000000000001569 <+57>:    movaps 0x27b0(%rip),%xmm0        # 0x3d20
   0x0000000000001570 <+64>:    movaps %xmm0,-0x50(%rbp)
   0x0000000000001574 <+68>:    movb   $0x0,-0x10(%rbp)
   0x0000000000001578 <+72>:    call   0xcd0 <getKey@plt>
   0x000000000000157d <+77>:    mov    %rax,-0x78(%rbp)
   0x0000000000001581 <+81>:    lea    -0x50(%rbp),%rax
   0x0000000000001585 <+85>:    mov    %rax,-0x80(%rbp)
   0x0000000000001589 <+89>:    mov    -0x80(%rbp),%rdi
   0x000000000000158d <+93>:    mov    -0x78(%rbp),%rsi
   0x0000000000001591 <+97>:    call   0xce0 <AES_128_ECB_PKCS5Padding_Decrypt@plt>
   0x0000000000001596 <+102>:   mov    %rax,-0x88(%rbp)
   0x000000000000159d <+109>:   mov    -0x88(%rbp),%rdi
   0x00000000000015a4 <+116>:   call   0xcf0 <free@plt>
   0x00000000000015a9 <+121>:   mov    -0x78(%rbp),%rdi
   0x00000000000015ad <+125>:   call   0xcf0 <free@plt>
   0x00000000000015b2 <+130>:   mov    -0x88(%rbp),%rax
   0x00000000000015b9 <+137>:   mov    %fs:0x28,%rcx
   0x00000000000015c2 <+146>:   mov    -0x8(%rbp),%rdx
   0x00000000000015c6 <+150>:   cmp    %rdx,%rcx
   0x00000000000015c9 <+153>:   mov    %rax,-0xa0(%rbp)
   0x00000000000015d0 <+160>:   jne    0x15e6 <getPath+182>
   0x00000000000015d6 <+166>:   mov    -0xa0(%rbp),%rax
   0x00000000000015dd <+173>:   add    $0xa0,%rsp
   0x00000000000015e4 <+180>:   pop    %rbp
   0x00000000000015e5 <+181>:   ret    
--Type <RET> for more, q to quit, c to continue without paging--
   0x00000000000015e6 <+182>:   call   0xcb0 <__stack_chk_fail@plt>
End of assembler dump.
(gdb) x/s 0x3d20
0x3d20: "tmhnj20r1KJPexqEWFEk+x4taM6wVJh5wK9YHkB0mAzlzisYIoO4q+P/2OCpHOWr"
(gdb)
```
Now, we use any online decryption tool:

![image](https://user-images.githubusercontent.com/73381089/167369727-3179bf7b-b831-4086-bdc6-2f962f614318.png)

And voila, turns out the path indeed was the flag: ctf{y0u_f0Und_th3_m@lic10uS_d0m@in}
