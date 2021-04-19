# XSS Writeup

XSS is a type of vulnerability in which we can inject JavaScript code into a browser. This injected code is then interpreted by the browser. 

JavaScript can do the following: 
- Steal cookies
- Send HTTP requests to websites
- Modify websites

Attackers can use any of these capabilities to their advantage. 

There are 3 main types of XSS vulnerabilities: 
- *Reflected*: For data not stored in a database, just in runtime. 
- *Stored*: For data stored in a database. 
- *DOM*: An attack that never touches the server, staying on the client side. 

## Recon 1
When I try to log in with username Jamie and password password, I receive the following error: 

`Error: No username: Jamie`

Interesting. It is almost always bad practice to tell your user if their username or password is wrong in the case of a bad authentication. Why? Because this makes things easier for attackers...I don't think that's what they were going for in this lesson, though! 

Well, sort of. It looks like the username being displayed back shows a potential XSS vulnerability. 

Next, we enter the following as a username, to test for XSS: `<script>alert("XSS");</script>` and whatever we want for the password. 

Sure enough, we get a popup window that says "XSS". Well, okay then. 

## Weaponization and Delivery 1
To do a super basic XSS attack that doesn't do anything other than alert us to XSS, we do the following: 

`http://sandbox-hackedu.com/signin?username=<script>alert("XSS");</script>&password=a`

We can do all sorts of things with JavaScript, such as keylogging and other attacks. 

## Reconnaissance and Weaponization 2

After signing in with the given credentials, the blog comments that were already there made me chuckle. 

I created a normal comment, "Your Mom", as alice. 

Then, I tried a comment with the standard `<script>alert("XSS");</script>`, feeling very much like a Russian spambot on LiveJournal. Fun fact, it didn't pop up with a window. BUT, it did get rid of the script tags...Which means that some sort of XSS filter is installed. 

The excellent [OWASP](https://owasp.org/www-community/xss-filter-evasion-cheatsheet) has a cheat sheet for XSS filter evasion. Let's try this one: 

`<IMG SRC="javascript:alert('XSS');">`

Nope, doesn't work. It just can't find the image. 

Maybe...this? `<IMG SRC=javascript:alert(&quot;XSS&quot;)>` Haha, nope. We're going down the wrong road with images here. 

We try a malformed `a` tag as follows: `\<a onmouseover="alert(document.cookie)"\>xxs link\</a\>` and in fact, we DO get a popup, though it doesn't say XSS. This actually started infinite looping, and I had to reset the sandbox. 

After the second hint, we try `<scrscriptipt>alert("XSS");</scrscriptipt>`. And...IT WORKS! 

## Weaponization and Delivery 3 / Exploitation 3

An XXS attack that alerts with cookie data, you say? Let's try this: `<scrscriptipt>alert(document.cookie);</scrscriptipt>`

Yep. The first window just says XSS. But the second one gives us cookie information. 

## Weaponization 4

We reset the sandbox, then register with username `<script>alert("myname");</script>` and password `lmao`. We sign in. Now when we post a comment, we see the message `myname` in a popup window. 

## Defense 

To defend against XSS, we want to use proper escaping, avoid passing data to JavaScript, and add security headers. 

In Java, we could do: 

```
import static org.apache.commons.lang3.StringEscapeUtils.escapeHtml4;
// and...
String clean_var = escapeHtml4(original_var);
```

## Patching the Vulnerabilities 

We reset the sandbox, and add the following lines of code to the `try` block:

```
username = escapeHtml4(username);
comment = escapeHtml4(comment);
```
Also, the password is stored in plaintext again. Lol. 

The tests pass, and the sandbox is patched! While logged in as Alice, we try to comment with `<scrscriptipt>alert("XSS");</scrscriptipt>`. While it looks like there is still *some sort* of filtering going on, we can no longer exploit the vulnerability! 

# Conclusion 

This was a good lesson. Everything was well-written and I was able to obtain all the information that I needed. 
