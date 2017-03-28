# RSA 2 - 80 points

Some more RSA! This time, there's no P and Q... [this](https://github.com/EasyCTF/easyctf-2017-problems/blob/master/rsa2/ciphertext2.txt). 

```
n: 360863815763347129786223223753677186553479
e: 65537
c: 212985665370717183529999720499957413167571
```

### Solution

RSA 2 is like [RSA 1](rsa-1-50-points.md), with the difference that this time we don't have the factorization of *N*.
*N* is small enough, though, that it's easy to factor.
For example, https://www.wolframalpha.com/input/?i=prime+factorization+of+360863815763347129786223223753677186553479
gives us:

 * *p* = 595630980471473199937
 * *q* = 605851320019829172167

Now we can just reuse any decryption code from [RSA 1](rsa-1-50-points.md)
to find the private key *d*:

 * *d* = *e*<sup>−1</sup> mod (*p*−1)(*q*−1) = 300201340241660215082768288814375000466433

Using *d*, we decrypt the ciphertext as

 * *M* = *c*<sup>*d*</sup> mod *N* = 136143999223212922673501593241914978429

In hexadecimal,

 * *M* = 0x666c61677b6c30775f6e5f616438307d

Interpreting each pair of hexadecimal digits as an ASCII character
(`66`→'f', `6c`→'l', etc.) reveals the flag:

```
flag{l0w_n_ad80} 
```

After the competition, the
[grader.py script](https://github.com/EasyCTF/easyctf-2017-problems/blob/master/rsa2/grader.py)
showed that flags were perhaps randomly generated
as `l0w_n_` followed by a four-character random string.

### External Writeups
