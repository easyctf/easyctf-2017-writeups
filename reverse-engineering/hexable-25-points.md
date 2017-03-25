# Hexable - 25 points

I tried to hide a flag sneakily, can you find it? [Download](https://github.com/EasyCTF/easyctf-2017-problems/blob/master/hexable-autogen/hexable)

### Solution

The challenge file was a small executable of 235 bytes:

```
$ file hexable
85b33e8a48a9e129d23c20483b4b12cd1d199708_hexable: ELF 32-bit LSB executable, Intel 80386, version 1 (GNU/Linux), statically linked, stripped
```

[strings](https://en.wikipedia.org/wiki/Strings_%28Unix%29) was lucky and found a flag in plaintext.

```
$ strings -a hexable
Can you find the flag?
easyctf{PT4z0pqN1xQuXm}
```

### External Writeups
