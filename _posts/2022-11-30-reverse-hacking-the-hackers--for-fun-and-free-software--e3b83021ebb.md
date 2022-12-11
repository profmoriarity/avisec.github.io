---
layout:	"post"
categories:	"blog"
title:	"Reverse Hacking the hackers: For fun and free software?"
date:	2022-11-30
thumbnail:	/img/1-5cOYZvdQuTupK-3hblkNA.jpeg
author:	siva
---

* * *

It's been a long time writing something on security stuff. But I would like to
share one of my experiences. This blog post can be a fun read for people who
are already into security and quite surprising and enlightening for people who
aren't into security.

For hackers like me, fulfillment comes by doing actual hacking and these
opportunities do not come always because of ethics and legality.

![](/img/1-5cOYZvdQuTupK-3hblkNA.jpeg)

Though it's been more than a year since this happened, it all started when a
friend came to ask to check why some things are not working on his laptop. My
quick question was "what did you install?" and the obvious response was "X
cracked software".

Typical instructions to install pirated software:

  *  _Please turn off your antivirus(very very important)_
  *  _Open a command prompt as administrator and run the setup.exe_

When you follow these steps, congratulations, you got yourself free software
and a company in your digital life.

So looking at the infected laptop, I can see that the antivirus is "disabled
by administrator" and there are small windows hiding on the desktop.

Checking the startup items, there are a few .exe files that run on startup. In
the list of processes, there is a process running that is using a considerable
amount of network.

Taking these binaries and doing some string analysis using Ghidra, I found an
IP address that I assumed must be the C2 server. I did ran an port scan on the
IP to discover many open ports amongst the interesting one for me the
webserver running at port 80.

Just navigating to the application running on port 80, reveals an login page
with title saying "OnyxDropper". A quick google search gave me what it is.

![](/img/17aHdH1Dgr1-L_oRFFVh53w.png)github page for OnyxDropper

This happens to be dropper with a web UI to quickly drop payloads to all the
victims and couple more extra features. The client is a Csharp executable
while the web application is a php based and listens to the client for
incoming data.

I am like, PHP? and I am 100% it has bugs. The description says the login
default credentials are root:root. However, no luck with that. Getting hands
on with php web code located at
<https://github.com/DKOnyx/OnyxDropper/tree/master/Server>. There are multiple
vulnerabilties identified.

![](/img/1WN5KBnOZsdVDbk8zGmrp6g.png)

Checking the login.php, the author has implemented some basic security by
adding htmlspecialchars() function on the username.

![](/img/1buyzxxvrQVKfwXzsumW9Gg.png)

The file set-command.php does not seem to verify any session and their
validation/filter on the post variable "command". Checking the add-
payload.php, I can see there is a file upload feature but this file is
validating the session to perform the upload.

The feature is probably an option to host their custom payloads to easily send
them to the victim's machine.

![](/img/1X7eQSvG_8niuwdN6AqH63Q.png)

Exploiting both SQL injection and the file upload features, I got a remote
code execution on the machine.

Moving further I have created a new user " _Defaultuser_ " on the machine and
added it to the "Remote Desktop Users". And yeah 3389 port is open to the
internet.

Upon connecting the machine, I could then see another user is already
connected. They seem to be using
AsyncRAT(<https://github.com/NYAN-x-CAT/AsyncRAT-C-Sharp>).

And around 500+ active victims are connecting to RAT at any given time. Just
to give an idea, the Hacker can

  * watch all victims' live camera feeds all the time
  * log everything they are typing
  * get a snapshot/live stream of their screen
  * download all chrome passwords, cookies, history
  * capture credit card information

In fact, they already did dump all the passwords and data. The count was more
than 2000 victims from countries India, Brazil, Australia, the USA, Canada,
and many others.

![](/img/1Ya1VPMJZxrphTWEGkKOqug.png)![](/img/19_8LOzqjFWVA_qUxk1mUdQ.png)

This contained credentials and access to 1000s of public/private applications.

Trying to get to know further about their real identity. I checked the apache
access logs to check the IP address of the users trying to login into this
dropper web UI. And the IP address geo locates in West Bengal in India.

One more step forward, I have created a payload and replaced the AsyncRAT.exe
with the payload. Hoping to get a reverse RDP connection and monitor the
hacker's moves much closer to potentially get his real details. But I did a
typo in giving the payload parameters and it messed up everything on the first
attempt. The hacker was sad that the async rat was not working, but
reinstalled a new setup.

On the second attempt, I did create the right payload and but for this type he
was really scared that something has happened and deleted the whole server. I
continued to monitor the IP for more than 6 months and it never again listened
for victims on those default async rat ports.

I could have tried other techniques like dumping the hash and PTH and stuff.
But really at that time, I was a real noob to this window's exploitation and
stuff.

Still, I think it was a good pwn as the attacker had to delete the server.

### Moral

The Internet gives you all the free stuff, please do the reason why somebody
has to give it for free to you.

  * Never ever install pirated software -- it's risky and also illegal to do so.
  * Use a password manager -- both paid and open source available(Bitwarden is a good one) -<https://www.youtube.com/watch?v=30QqIeb1Pu4>
  * I think YOUR internet privacy is NOT just yours -- it's also the ones of your friends and family.

Okay, that's the end of the pwn story. I wrote down what I could remember.
Hope it was fun reading.

#### About Me

I am a cybersecurity consultant. I think hacking is not just about hacking
computers and software but is about finding loopholes in everything.

I also do bug bounty on the Hackerone platform hunting for bugs on private and
public bug bounty programs. Though most of my work is in web hacking, need of
the hour -- I can do anything and everything to find those hacks.

Feel free to connect with me on my Twitter <https://twitter.com/le4rner>

Read my old blogs

  * How I earned 60K+ from a private program -- <https://medium.com/@sivakrishnasamireddi/how-i-earned-60k-from-private-program-71bd51554490>
  * Just another tale of severe bugs on a private program -- <https://medium.com/@sivakrishnasamireddi/just-another-tale-of-severe-bugs-on-a-private-program-405870b03532>

Writing this blog gave me extra motivation to be a part of the cybersecurity
world.

