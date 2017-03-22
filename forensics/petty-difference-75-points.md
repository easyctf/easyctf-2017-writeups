# Petty Difference - 75 points

I found two files in a secret room. They look like jumbled letters with no patterns. I mean look at it! [file1](https://github.com/EasyCTF/easyctf-2017-problems/blob/master/petty-difference/file1.txt) is identical to [file2](https://github.com/EasyCTF/easyctf-2017-problems/blob/master/petty-difference/file2.txt), right?

### Solution
###### Writeup by Valar Dragon

Since it mentions the files being identical, lets look at the differing bytes!

``` python
a = open('file1').read()
b = open('file2').read()
c = ""
d = ""
for i in range(len(a)):
    if(a[i]!=b[i]):
        c += a[i]
        d += b[i]
print(c)
print(d)
```

which gives the flag!
``` bash
$ python3 solve.py
easyctf{th1s_m4y_b3_th3_d1ff3r3nc3_y0u_w3r3_l00k1ng_4}
lfbz95eobcrtqadt7kdxz0dcisw{x5kik4pueriiebmavwnxdvwex9
```

### External Writeups

* [https://github.com/HackThisCode/CTF-Writeups/blob/master/2017/EasyCTF/Petty%20Difference/README.md](https://github.com/HackThisCode/CTF-Writeups/blob/master/2017/EasyCTF/Petty%20Difference/README.md)
