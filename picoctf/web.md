## Forbidden Paths

**Flag: `picoCTF{7h3_p47h_70_5ucc355_6db46514}`**
**Difficulty:** `easy`

This was a simple challenge based on understanding of file paths. The webpage had a portal that could only read relative file paths.

![](/media/web_forbiddenpaths_1.png)

Since it was mentioned that the website files are in the `/usr/share/nginx/html/` directory, simply we could write the relative path for `/flag.txt` as `../../../../flag.txt`, which gave the flag.

![](/media/web_forbiddenpaths_2.png)

**Learnt:** file paths in linux


---


## Cookies

**Flag:`picoCTF{3v3ry1_l0v3s_c00k135_bb3b3535}`**
**Difficulty:** `easy`

On reading the challenge, we had been given a web portal with one form field, where my initial guess was to enter different values, and see how the "cookie" in the "Application" tab of the webpage changed.

![](/media/web_cookies_1.png)

I noticed that on entering cookie names, the value of the cookie changed to a different integer. We also a got a banner which said "That is a cookie! Not very special though..." which meant that we were going in the right direction, and had to look for the appropriate cookie value.

![](/media/web_cookies_2.png)

So I tried to set the `value` for the cookie, in a range from `1` to `18`. 

![](/media/web_cookies_3.png)

Finally, on setting the cookie `value` to `18`, the website displayed the flag in plaintext.

![](/media/web_cookies_4.png)

**Learnt:** cookie manipulation

**Challenges/Wrong Approaches:**
Since it was time consuming to manually try to change every variable, I tried to execute a script in the console using JS to try out all cookie values from 0 to 50, but that did not work, so I had to manually change cookie values one by one. I was able to figure out that there are only 30 possible values through elimination.

---

## SOAP

**Flag: `picoCTF{XML_3xtern@l_3nt1t1ty_e5f02dbf}`**
**Difficulty:** `medium`

On opening the web portal and using `Inspect Element`, we find a file in the sources called `xmlDetailsCheckPayload.js`. On further searching for XML vulnerabilities, we are pointed to a XXE injection. 

![[web_soap_1.png]]

Then, under the `Network` tab in `Inspect Element`, we monitor traffic and observe that on pressing the details button on the web portal, a `POST` request is made, called `data`. 

![[web_soap_2.png]]

Then, when we use the standard payload to exploit XXE to obtain files. In this case, we're trying to obtain the contents of the `/etc/passwd` file, so the payload is:

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE data [
<!ENTITY file SYSTEM "file:///etc/passwd"> 
]>
<data>
<ID>
&file;
</ID>
</data>
```

On passing this payload, the data file now updated to contain the flag.

![[web_soap_3.png]]

**Learnt:** XXE injection, burp suite

**Challenges/Wrong Approaches:** I had to figure how to send `POST` requests through burp suite, but got no response. So I tried to use the `Network` tab to edit the `POST` request and send it with the modified payload instead.

**References:** 
https://portswigger.net/web-security/xxe
https://en.wikipedia.org/wiki/SOAP





