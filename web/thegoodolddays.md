# Solution for TheGoodOldDays

On opening the website given in the challenge, we get the message: 
"Wrong Information, Our Community Members use Old Windows Laptops. You are using a New Linux"

How does the server know we are using "New Linux"? Probably through the user-agent header sent by my browser in the GET request sent to the server.

On firefox, we can press "Ctrl+Shift+c" to open up the inspector window, navigate to the "network" tab, click on the 200 GET request sent to the server,
and examine the headers sent by our browser. 

![Screenshot_20220509_024630](https://user-images.githubusercontent.com/73381089/167316097-26d9602a-dd89-4e90-9f88-f69e36a46103.png)

My current user agent seems to be: "Mozilla/5.0 (X11; Linux x86_64; rv:100.0) Gecko/20100101 Firefox/100.0".
But the server wants an old windows one. A simple google search gave "Mozilla/4.0 (compatible; MSIE 5.5; Windows NT 5.0)" user-agent which was used
by the Internet Explorer 5.5 on Windows 2000. 

![Screenshot_20220509_023748](https://user-images.githubusercontent.com/73381089/167316065-8c3d9efa-490f-43e6-a770-ac6705a01c3e.png)

We can click on "Edit and Resend" button in "Headers" tab when examining the GET request, to modify the user-agent before resending it to the server.
So, we change the user-agent to the old windows one, send it, and examine the response, which gives us the flag: CTF{us3r_a93nt_s_uzlss}
