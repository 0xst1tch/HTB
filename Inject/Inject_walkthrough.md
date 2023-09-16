# Inject

As always we start by checking the connexion to the machine:

![Untitled](Inject_imgs/Untitled.png)

Now we try to get all open ports within the machine:

![Untitled](Inject_imgs/Untitled%201.png)

We see an http server on port 8080, so we access via web:

![Untitled](Inject_imgs/Untitled%202.png)

We check the functionality of the site and discover that we can upload files to the server

![Untitled](Inject_imgs/Untitled%203.png)

Upload a file and capture the request with burpsuit

![Untitled](Inject_imgs/Untitled%204.png)

Change the request in order to get /etc/password

![Untitled](Inject_imgs/Untitled%205.png)

We get users phil and frank.

Now we get some info about the server 

![Untitled](Inject_imgs/Untitled%206.png)

And see is using spring, so we need to look for some CVE’s for spring.

![Untitled](Inject_imgs/Untitled%207.png)

Here we see versions of spring and we look for posible [CVE’s](https://spring.io/security/cve-2022-22963)

![Untitled](Inject_imgs/Untitled%208.png)

To exploit this machine we will try to upload  a revershell payload to the server, to do that we see that we are able to make post to /functionRouter so we create a payload:

![Untitled](Inject_imgs/Untitled%209.png)

And now we try to upload it, to do so we need a http server running local:

![Untitled](Inject_imgs/Untitled%2010.png)

And force the server to execute curl:

![Untitled](Inject_imgs/Untitled%2011.png)

Now we have our payload updated in the server:

![Untitled](Inject_imgs/Untitled%2012.png)

So now we are going to force the server to exec it

![Untitled](Inject_imgs/Untitled%2013.png)

but before we need to listen on the port 8884 to get the reverse shell.

![Untitled](Inject_imgs/Untitled%2014.png)

Now we are in as user frank, but we have no flag here so we need to move. We are going to exploit privesc. So what we have to do is create another payload locally and from frank shell download it:

![Untitled](Inject_imgs/Untitled%2015.png)

With this payload in frank dir:

![Untitled](Inject_imgs/Untitled%2016.png)

We wait a bit and execute bash -p:

![Untitled](Inject_imgs/Untitled%2017.png)

And now as root we get both creds.