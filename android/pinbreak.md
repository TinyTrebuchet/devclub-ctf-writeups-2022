# Solution for PinBreak

Firstly, we unzip the apk `unzip pinbreak.apk`. On checking out the files in the apk, we see a sqlite3 database file: assets/pinlock.db 
and we examine the database:

![image](https://user-images.githubusercontent.com/73381089/167358559-952e4426-d50e-4e6e-bd73-f7fc63ab9356.png)

We also see a README in the same directory:

```
v1.0:
- Pin database with hashed pins

v1.1:
- Added AES support for secret

v1.2:
- Derive key from pin
[To-do: switch to the new database]
```

Now to view the source code of the apk, we first convert the apk to a jar file using a tool "dex2jar": `dex2jar -f -o output.jar pinbreak.apk`.
And then we use a tool "jd-gui" to view decompiled jar file: `jd-gui output.jar`.

Taking a quicklook at com.pikachu.pinbreak/MainActivity.class shows that the app takes an input from us, computes the SHA-1 hash and compares it 
with the hashed pin stored in the database: `bae5c9d883433f2d1e926ef693831dafa1664306`.

![image](https://user-images.githubusercontent.com/73381089/167359126-27ac3496-00c6-4387-9c2e-72f1878a9dce.png)

We can just try to reverse lookup the SHA1 hash, and since the pin should a 4 or 6 digit long number, it should be easy.

![image](https://user-images.githubusercontent.com/73381089/167359845-d1aac3bd-411f-4ff9-b0e3-a58b354a05b4.png)

Voila, we get the PIN. We enter it in the installed app now to get the flag: ctf{Th@ts_H0w_y0U_br3@k_p!ns}
