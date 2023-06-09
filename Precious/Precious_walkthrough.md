# Precious

# Information gathering

We start by checking the connexion with the machine: 

![Untitled](Precious_imgs/Untitled.png)

And once we know we have connexion we can perform a enumeration using nmap:

![Untitled](Precious_imgs/Untitled%201.png)

We see open ports 22(ssh) and 80(http), and see that port 80 have a web server [http://precious.htb](http://precious.htb) so we add this to our /etc/hosts and in firefox go to the web page:

![Untitled](Precious_imgs/Untitled%202.png)

We see that is a conversor from web to pdf, we will check the functionality by passing a web to them. We should host a web server using python:

![Untitled](Precious_imgs/Untitled%203.png)

And use this server in the web page, this will download a pdf.

![Untitled](Precious_imgs/Untitled%204.png)

To see more about the document we can check the metadata by using exiftool:

![Untitled](Precious_imgs/Untitled%205.png)

The interesting part here is the Creator, where we discover that is created by pdfkit v0.8.6 and we can check if exists a exploit for this.

![Untitled](Precious_imgs/Untitled%206.png)

We can see that the library is vulnerable to command injection: “an application could be vulnerable if it tries to render a URL that contains query string parameters with user input” and “if the provided parameter happens to contain a URL encoded character and a shell command substitution string, it will be included in the command that PDFKit executes to render the PDF”.

So now we will try to abuse this vulnerability to get a reverse shell,  in order to do this we will use [revshells.com](http://revshells.com/) and create a payload to add to the URL submitted to the web page:

![Untitled](Precious_imgs/Untitled%207.png)

![Untitled](Precious_imgs/Untitled%208.png)

And with this we will get connexion to the server:

![Untitled](Precious_imgs/Untitled%209.png)

Locking around the home directory we see two users directories:

![Untitled](Precious_imgs/Untitled%2010.png)

And in henry directory we find the user.txt flag, but as user ruby we have no permission to read it:

![Untitled](Precious_imgs/Untitled%2011.png)

So we look around a little more and found the credentials of user henry:

![Untitled](Precious_imgs/Untitled%2012.png)

So we try to login as henry

![Untitled](Precious_imgs/Untitled%2013.png)

Now we need to escalate privileges and get root. As user henry execute sudo -l, to see the commands that can be executed as root, and discover   

![Untitled](Precious_imgs/Untitled%2014.png)

Now check the code and see that it use YAML.load() that is vulnerable function. 

Now we need to generate a yml payload to be load in the code an let us execute as bash as root:

![Untitled](Precious_imgs/Untitled%2015.png)

This yml payload will allow us to get a root bash, so now we need to execute the dependencies update: 

![Untitled](Precious_imgs/Untitled%2016.png)

Now /bin/bash have sudoers permission:

![Untitled](Precious_imgs/Untitled%2017.png)

And can be executed as root:

![Untitled](Precious_imgs/Untitled%2018.png)