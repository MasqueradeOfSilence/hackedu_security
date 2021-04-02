# XXE Writeup
The first exercise in this module was an AJAX request. I originally could not get it working, but upon checking my work, I found that I needed to remove the XML header for it to work. 

```
<!DOCTYPE foo [ <!ENTITY hello SYSTEM "file:///etc/passwd" > ]>
<Search><SearchKeyword>&hello;</SearchKeyword></Search>
```

The next exercise was a file upload. Initially, I neglected to embed the code properly into the Users section. Once I did that, it worked properly. 

[Source code for file upload](xxe_upload.xml)

Then I completed the network mapping exercise. At first, I tried to be a hero and scan all ports in a single file, but this was not possible. So after separating them out into individual files, I found that I was able to grab the secret from port 4002.

[Source code for network mapping](network_xxe_mapping_2.xml)

The final exercise was code patching. In order to patch the sandbox, all I had to do was add the following to disable external entities altogether: 
```
dbFactory.setFeature("http://apache.org/xml/features/disallow-doctype-decl", true);
```
This fixed the sandbox and the vulnerability. 

Overall, I enjoyed this module. The biggest challenge was proper syntax, which I think will become less of an issue as I gain more experience with XML. 
