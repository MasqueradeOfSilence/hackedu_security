# Using Components with Known Vulnerabilities: Writeup

Most of us use third-party or open-source software as part of our applications. This is great in terms of functionality. But if one of these packages is found to have a vulnerability, attackers may use this vulnerability in order to exploit *your* software which is dependent on it. 

## Reconnaissance 
We enter in the valid RSS feed and it works as expected. We then try entering `abcd` and it gives us a `KeyError: content`. We can see that it is a WSGI application, which uses Python. We can even see code snippets, because the errors weren't hidden from the user. 

We can see that the code is using a Python `feedparser` of some sort. As instructed by the tutorial, we look up "feedparser vulnerabilities". [Here we go](https://snyk.io/vuln/pip:feedparser). We see both DoS and XSS vulnerabilities. 

Through the same link, we see that feedparser 5.0 has 4 vulnerabilities and that it was released on January 28, 2011. Well, when we go back to the first page and log in, we see `© RSS 2011`. So, this is probably that same vulnerable version. The vulnerabilities are listed [here](https://www.cvedetails.com/vulnerability-list/vendor_id-11368/product_id-20651/version_id-106904/Mark-Pilgrim-Feedparser-5.0.html) -- two DoS and 2 XSS vulnerabilities. 

## Weaponization and Delivery
The tutorial suggests trying an XSS attack. So, we try plugging in `<script>alert("XSS");</script>` into the RSS comment box. This doesn't work. 

A quick glance (but not too close) at the next page makes it seem like we need to embed this into the actual RSS feed syntax so that way we don't get any errors! So, try `<rss xmlns:content="http://www.mywebsite.com/rssfeed/" version="2.0"> <channel> <item> <content:encoded>RSS Content<script>alert("Hello XSS!");</script></content:encoded> </item> </channel> </rss>`.

This is filtered out. Try `<rss xmlns:content="http://www.mywebsite.com/rssfeed/" version="2.0"> <channel> <item> <content:encoded>RSS Content<scrscriptipt>alert("Hello XSS!");</scrscriptipt></content:encoded> </item> </channel> </rss>`. This doesn't do anything either. 

Upon peeking at the hint more closely, it seems like we need to put the script inside of the URL itself. This is because that is what will be placed to the screen. 

```
<rss xmlns:content="http://www.mywebsite.com/rssfeed/<script>alert(\'HelloXSS\');</script>" version="2.0"> <channel> <item> <content:encoded>RSS Content</content:encoded> </item> </channel> </rss>
```

Finally, after messing around a lot with syntax, we get the alert window popping up! 

## Defense
It is suggested to use regularly updated open source projects, document which third-party/open-source software you use, and to continually update to the latest version of each piece of software. 

## Conclusion
This would've been a bit easier with more knowledge on how RSS works, but overall it was a great and informative section. 