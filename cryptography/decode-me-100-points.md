# Decode Me - 100 points

Someone I met today told me that they had a perfect encryption method. To prove that there is no such thing, I want you to decrypt this [encrypted flag](https://github.com/EasyCTF/easyctf-2017-problems/blob/master/decode-me/encrypted_flag.txt) he gave me.

### Solution
###### Writeup by Valar Dragon

The input file ends in the iconic `=` sign, hinting at base64.
And it b64 decodes, into another base64 string. So I wrote a short python3 script to perform repeated base64 decryption

``` python
import base64

a = open('begin').read()
a = a.replace('\n','').replace('\\n','')
b64 = str(base64.standard_b64decode(a),'utf-8')

while 'easyctf' not in b64:
    b64 = str(base64.standard_b64decode(b64),'utf-8')
print(b64)
```

which gives the flag
``` bash
$ python3 solve.py
easyctf{what_1s_l0v3_bby_don7_hurt_m3}
```


### External Writeups

* [https://github.com/HackThisCode/CTF-Writeups/blob/master/2017/EasyCTF/Decode%20Me/README.md](https://github.com/HackThisCode/CTF-Writeups/blob/master/2017/EasyCTF/Decode%20Me/README.md)
