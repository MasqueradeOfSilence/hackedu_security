# Writeup for Security Misconfiguration 

## Reconnaissance 1
- Upon examining the response on the homepage, I see...no authentication information
- I can see that the server is Ubuntu 
- We have a form with an action to do sign in.

Information: The instructions inform us that my second bullet point was the most important one. We can see the web server and version number. 

[Here is a vulnerability that I found, which has to do with remote code execution.](https://www.cvedetails.com/cve/CVE-2014-0133/)

## Defense 1

The page says to "check the header information on your responses and suppress all information leakage from the server." But, it does not have a code editor button. 

After messing around with it for a while, I realized that this was probably more of a general principle they are trying to teach us to do, as opposed to an exercise. It is not always clear in this tutorial. 

Also, plaintext responses, really? *inputEmail=yourmom&inputPassword=pizza* Well THANKS for potentially exposing my fake password! :P Never mind that nCrack could grab it in like 2 seconds...

I wonder if they will talk about that later in the tutorial...


## Reconnaissance 2

When we log in with username alice and password bob', we get a pymysql.err.ProgrammingError. In fact, there is a LOT of useful information in there...Quite in-depth. 

In fact, it is a stack trace. 

## Defense 2

The solution to this is to run all applications in production mode, with stack traces and debug statements turned OFF. 

## Reconnaissance 3

I tried the following URLs to see information: 

- http://sandbox-hackedu.com/html: Not Found error
- http://sandbox-hackedu.com/templates: Not Found error
- http://sandbox-hackedu.com/js: Got us a minified Jquery file!
- http://sandbox-hackedu.com/javascript: Not Found error
- http://sandbox-hackedu.com/files: Not Found error 
- http://sandbox-hackedu.com/data: Not Found error
- http://sandbox-hackedu.com/root: Not Found error
- http://sandbox-hackedu.com/public_html: Not Found error

After that I stopped because it's a simulation after all and they were probably only going to have a grand total of one (1) error. 

## Defense 3

Yes, that was all there was. The solution to this generally involves configuration files on the server. We do not want to list files in a web server directory. 

## Reconnaissance 4

- http://sandbox-hackedu.com/wp-admin: Not Found error
- http://sandbox-hackedu.com/admin: This takes us to an admin login page! 

## Weaponization and Delivery 4 / Exploitation 4

At first, I tried WordPress' default credentials. These can be found [here](https://varyingvagrantvagrants.org/docs/en-US/default-credentials/). The username should be admin, and the password should be...password. 

However, I tried that and it didn't work. Lies. 

Why? Because it isn't a WordPress admin page. The hint says to use the software type and version. Which is nginx/1.3.15 (Ubuntu). 

Well, username ubuntu and password ubuntu don't work either. Neither did ubuntu/password. 

The second hint said that the software is WebMailPro 7.6.1, which is more helpful. It is in the HTML: <title>WebMail Pro 7.6.1 Admin Panel</title> 

So, we check (this)[https://afterlogic.com/docs/webmail-pro-net/adminpanel-guide/login-screen] and use the default credentials of mailadm/12345. This would have been a really bad idea to brute force lol, regardless of what the prompt says. 

After using those credentials, we successfully accessed the admin's account. 

## Defense 4

Never use default credentials! And never broadcast what version of software you are using! 

# Conclusion

After the fourth exercise, I had finished the module. Other than the one somehwat obfuscated instruction set at the beginning, everything was fun and (fairly) straightforward here. Some really valuable information. 
