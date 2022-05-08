# Solution for UnChangeablePasswords

On checking out the website given in the challenge, we see that it's a simple website with "login", "sign up" and "change password" features.

However, upon inspecting the POST request sent for login, we see that the response from server doesn't set any cookie (and hence session) for us.

![Screenshot_20220509_025518](https://user-images.githubusercontent.com/73381089/167316477-a8bfe857-bd81-49bf-aee6-4b2cf8a10705.png)

This has an important implication. Whenever we change our password, we send a POST request with our username and new password to the server.

![Screenshot_20220509_025555](https://user-images.githubusercontent.com/73381089/167316496-41655b48-f418-40a6-ad49-85c1eeab110d.png)

Since the server doesn't use sessions, it has no way of telling whether the person sending the "change password" POST request is actually the 
user or some impostor. Hence, we can exploit this to change the password of "admin", by editing and resending the request.

![Screenshot_20220509_025625](https://user-images.githubusercontent.com/73381089/167316499-08593443-c0b1-4d66-9b31-bc96beddb763.png)

Now, we can login as "admin" with the newly set password, and we get the flag: CTF{Pr311y_bas1c_1D0R} 
