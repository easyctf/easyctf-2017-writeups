# Scisnerof - 70 points

I found weird file! [elif](https://github.com/EasyCTF/easyctf-2017-problems/blob/master/scisnerof/elif)

### Solution
###### Writeup by Valar Dragon

Scisnerof is Forensics backwards, and elif is file backwards! It seems likely that elif is a reversed file.
This "elif" file has no default app, so let us look at it through a hex editor. I used Bless, a hex editor for linux.

![hexdump.png](https://raw.githubusercontent.com/HackThisCode/CTF-Writeups/master/2017/EasyCTF/scisnerof/hexdump.png)
If we scroll to the bottom, we see that theres the PNG File header reversed to GNP!

So it looks we just need to reverse the file!
We can do it in one line with python
``` python
$ python3
open('scisnerof.png','bw+').write(open('elif','br').read()[::-1])
```

Then open up [scisnerof.png](https://raw.githubusercontent.com/HackThisCode/CTF-Writeups/master/2017/EasyCTF/scisnerof/scisnerof.png),
![scisnerof.png](https://raw.githubusercontent.com/HackThisCode/CTF-Writeups/master/2017/EasyCTF/scisnerof/scisnerof.png)
and theres our flag! `easyctf{r3v3r5ed_4ensics}`


### External Writeups

* [https://github.com/HackThisCode/CTF-Writeups/blob/master/2017/EasyCTF/scisnerof/README.md](https://github.com/HackThisCode/CTF-Writeups/blob/master/2017/EasyCTF/scisnerof/README.md)
