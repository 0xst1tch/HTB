# Soccer

As always we start by checking the connectivity with the machine: 

![Untitled](Soccer_imgs/Untitled.png)

And now we try to get all open ports:

![Untitled](Soccer_imgs/Untitled%201.png)

We can see the domain hosted on port 80, we add this domain to our /etc/hosts and access to the web.

![Untitled](Soccer_imgs/Untitled%202.png)

We won’t get nothing from this interface so we will try to get some directories by using gobuster:

![Untitled](Soccer_imgs/Untitled%203.png)

So now we try to access /tiny dir:

![Untitled](Soccer_imgs/Untitled%204.png)

We can see that this is a Tiny file manager login, an opensource file manager, looking in the github repository of Tiny we can find the default credentials 

![Untitled](Soccer_imgs/Untitled%205.png)

So we will try to access using it.

And we get access:

![Untitled](Soccer_imgs/Untitled%206.png)

We can see here all the images used in the web and also the html code of the web page, into tiny folder we can find a php file with a user password:

![Untitled](Soccer_imgs/Untitled%207.png)

We can se that is possible to upload files, so that is what we going to do, we will create a php payload in order to get a shell in our machine:

![Untitled](Soccer_imgs/Untitled%208.png)

Now we upload this file and using netcat we start listening on port 4444:

![Untitled](Soccer_imgs/Untitled%209.png)

And access to his file uploaded using curl.

![Untitled](Soccer_imgs/Untitled%2010.png)

![Untitled](Soccer_imgs/Untitled%2011.png)

So now we are in as user www-data. We cant get nothing from here.

We go back to the open ports and see that in pot 9091 there is a web socket. The main difference between HTTP and WebSocket is that HTTP is built on a request-response model whereas WebSocket is like a Socket connection where both client and server can send data anytime asynchronously. So you can’t repeat the requests made in WebSocket as you would normally do for HTTP Requests in BurpSuite.

We google it and try to exploit it using sql injection based on [this](https://rayhan0x01.github.io/ctf/2021/04/02/blind-sqli-over-websocket-automation.html) report. We change the ws url of the script

![Untitled](Soccer_imgs/Untitled%2012.png)

And the data:

![Untitled](Soccer_imgs/Untitled%2013.png)

And now we run the script and start sqlmap pointing to localhost port 8081:

![Untitled](Soccer_imgs/Untitled%2014.png)

So the basic idea to solve this is:

1. have an HTTP Server script that will receive the SQLMap payload via GET parameter.
2. format the payload if needed (for example wrap it in a JSON format)
3. create a WebSocket connection to actual target, receive response and extract any token if needed.
4. Send SQLi payload and receive Output from WebSocket.
5. Display the output as response

![Untitled](Soccer_imgs/Untitled%2015.png)

And now sqlmap will make request to our localhost  http server on port 8081 where the script is running, and the script will forward this request to the web socket and get the response.

![Untitled](Soccer_imgs/Untitled%2016.png)

![Untitled](Soccer_imgs/Untitled%2017.png)

![Untitled](Soccer_imgs/Untitled%2018.png)

![Untitled](Soccer_imgs/Untitled%2019.png)

![Untitled](Soccer_imgs/Untitled%2020.png)

Now with this creds we try to connect using ssh:

![Untitled](Soccer_imgs/Untitled%2021.png)

Now we can see user flag and we try to escalate privileges. We can not use sudo -l, so we use:

![Untitled](Soccer_imgs/Untitled%2022.png)

And find all executables with suid. We see doas command and we access to its config. 

![Untitled](Soccer_imgs/Untitled%2023.png)

We can execute dstat as root without password.

The point here is to write a script in the dstat plugins folder that add the suid flag to /bin/bash:

![Untitled](Soccer_imgs/Untitled%2024.png)

Now we can execute bash as root

![Untitled](Soccer_imgs/Untitled%2025.png)