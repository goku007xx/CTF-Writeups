# SPA

Challenge Description:

> Enjoy a relaxing visit to our spa.

> https://spa.chall.pwnoh.io

## Analyzing the website

This seems to be a static website with not much to do. Looking in the network tab when refreshing the page, we can see a `isAdmin` API call happening periodically to `https://spa.chall.pwnoh.io/api/isAdmin`. The response to this request is always:
```json
{"isAdmin":false}
```

All we have to do is modify the response sent by the server to us to `{"isAdmin":true}`.

We can capture the request using burp and intercept the response to the request as well. If you wish to know how to do it using burp, this [link](https://onappsec.com/how-to-edit-response-in-burp-proxy/) will help you. 

Once done, we modify the json object and we get a new page with the flag.