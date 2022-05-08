# Solution for captchatheflag.md

The website given in the challenge displays an image of a string, and requires us to type that string withing 3 seconds and submit it.

To automate this process, we need to:
1) Retrieve the image
2) Pass it to an OCR, and convert it to text
3) Send back the text 

I wrote a bash script as follows for this:

```
#!/bin/bash

cd /tmp
curl https://web.ctf.devclub.in/dev/4/ > index.html
TT=$(grep 'name="tt"' index.html | awk '{print $3}' | sed s/value\=// | sed s/\"//g)
echo "tt = $TT"
grep 'base64' index.html | awk '{print $3}' | sed s/\"// | base64 -d > index.jpg
tesseract index.jpg text
TEXT=$(cat text.txt)
echo "text = $TEXT"

curl -d "tt=$TT&captcha=$TEXT" -X POST https://web.ctf.devclub.in/dev/4/
```

This script first gets the image by sending a GET request to the website via "curl", and greps the image data. The image data is base64 encoded,
and I needs to be decoded first before forwarding it to the OCR, which I do through "base64" command. Then, the image is converted to text via
the "tesseract" tool, and the text is sent as a POST request to the same website, along with the parameter "tt", the attribute which uniquely
identifies each image to the server, and is obtained in response of the GET request sent earlier.

Since the image is of low quality, the OCR might do erroneous conversions sometime, and you might need to re-run the script a couple of times.
Hence we get the flag: devctf{0k@y!_y0u_@re_@_hum@n}
