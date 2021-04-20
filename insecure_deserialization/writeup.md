# Insecure Deserialization Writeup

This vulnerability occurs as part of the serialization/deserialization process. Before deserializing something, you need to sanitize it. If you don't, you are at risk of an attacker executing malicious code. 

## Serialization
Following the principle of *abstraction* in software development, we create *classes* in code to represent informational structures, such as Users, Accounts, or Addresses, in human-readable format. However, while classes work well for local logic, they must be *serialized* into plaintext if information needs to be transferred. This is because transport protocols cannot transmit abstract classes. They need pure (plaintext) data. 

## Deserialization
With serialization, we convert class data into plaintext for ease of transport. However, we need to convert it back into class data after receiving it. Thus is the purpose of deserialization. 

If we deserialize the wrong information, though, attackers can use this to their advantage. They can execute malicious code, get past authentication guards, or run DoS attacks. 

## Reconnaissance 
Finding serialized objects is the first step. We click on `bin` and try to navigate to the folder, with `Intercept Requests` turned on. We see `Request to http://sandbox-hackedu.com/index.php?dir=O:3:%22App%22:3:{s:3:%22dir%22;s:4:%22/bin%22;s:6:%22action%22;s:4:%22view%22;s:5:%22files%22;a:0:{}}` in the header. It looks like `dir` is looking for serialized input. 

As noted in the description, the site uses a lot of PHP. So, I read through the information about PHP serialization. 

## Exploitation 
We have to figure out what the serialized object is being used for, so we know how to exploit it. It is also prudent to understand what type of data the serialized object is storing. 

`dir` seems like it could be referencing the selected directory, when we try to navigate somewhere in the file system. 

I was a bit stumped as to how to proceed from here, so I went to the example payload and used that: 

`http://sandbox-hackedu.com/index.php?dir=O:3:"App":3:{s:3:"dir";s:22:"/ > /dev/null;uname -a";s:6:"action";s:4:"view";s:5:"files";a:0:{}}`

Now this makes a little bit more sense (especially without all the ASCII-ified characters from the previous section). Essentially, we are replacing the `dir` command, which shows the table on the right-hand side, with a command to print the username. When we run it, the table is still displayed, but with the output of `uname -a`, showing us that there is a vulnerability for remote code execution (since we can run terminal commands). 

What if we try to see `/etc/passwd`? Let's try `http://sandbox-hackedu.com/index.php?dir=O:3:"App":3:{s:3:"dir";s:29:"/ > /dev/null;cat /etc/passwd";s:6:"action";s:4:"view";s:5:"files";a:0:{}}`. Note that we have to change the length to `s:29`, or else it will give us an `Invalid Argument` error. 

Once we do this, we can see the passwd file. 

Let's try it with `/etc/shadow` now, just as a bonus. Because if we get that, we could potentially decrypt some hashes (in a real environment; I'm not going to try it in this simulated one). `http://sandbox-hackedu.com/index.php?dir=O:3:"App":3:{s:3:"dir";s:29:"/ > /dev/null;cat /etc/shadow";s:6:"action";s:4:"view";s:5:"files";a:0:{}}` And sure enough, that works too. 

## Defense
The tutorial offers some good tips as to how to avoid these vulnerabilities: 
- Do not use serialized objects from untrusted sources. 
- Only store primitives in objects (note: this is rarely feasible in practicality)
- Validate data, maybe with an allowlist
- Use digital signatures
- Only serialize if absolutely necessary
- Follow the principle of least privilege, especially with the build environments in which you run your code. 

## Conclusion
This module was a bit more challenging than previous ones at the getgo, just because of syntax. But after resolving that, it wasn't too bad -- and it was cool to synthesize this with my pentesting knowledge. 