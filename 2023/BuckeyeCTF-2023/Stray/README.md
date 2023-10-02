# STRAY

Challenge Description:

> Stuck on what to name your stray cat?

> https://stray.chall.pwnoh.io/

## Analyzing the website

We have 2 links present which take us to different URL's.<br>
URL1 --> `https://stray.chall.pwnoh.io/cat?category=m`<br>
URL2 --> `https://stray.chall.pwnoh.io/cat?category=f`<br>  

According to the source code in `app.js`, the length of the GET variable `category` is being checked for a length of 1 and a random name is being taken from the files(m or f) and are being sent back as a response.<br>

Example response : `{"name":"Rue"}`

The goal is trying to get the flag contents from the previous dir using some sort of LFI but we have to bypass the length check first.

We can convert the GET param `category` to an array of length 1 so that the check can be bypassed. All we have to do is to add a `[]` in front of the get parameter in the URL. The below payload can be used to generate a `category` value of `['../flag.txt']`. We can run the below payload and get the flag.

Payload --> `https://stray.chall.pwnoh.io/cat?category[]=../flag.txt`