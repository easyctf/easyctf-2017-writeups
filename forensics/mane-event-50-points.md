# Mane Event - 50 points

My friend just got back from the plains and he took [this](https://raw.githubusercontent.com/EasyCTF/easyctf-2017-problems/master/mane-event/lion.jpg) picture with his new camera. He also told me there's a flag hidden in it - can you check it out for me?

### Solution
###### Writeup by Valar Dragon

![mane_event.jpg](https://raw.githubusercontent.com/EasyCTF/easyctf-2017-problems/master/mane-event/lion.jpg)

This is a great looking picture! Except it looks pretty normal, I doubt the flag has to do with manipulating pixels for only 50 points.

Lets just see if the flag is in there as plaintext!

``` bash
$ cat mane_event.jpg | grep easyctf
Binary file (standard input) matches
```

So the flag is in there as plaintext!
You can open your favorite hex editor, and search through the hexdump and try to find the flag, but you can also get the flag without leaving the command line!

``` bash
$ cat mane_event.jpg | hexdump -c | grep 'e   a   s' -A 2 | sed 's/ //g'
00016c0sts,easyctf{prid
00016d0e_in_african_eng
00016e0in33ring},2011B
```
Note that the reason for hexdump -c is so that grep doesn't interpret mane_event.jpg as a binary file. So we get the output with some spaces in between the chars! Then sed is removing the spaces.

Our flag is then:
`easyctf{pride_in_african_engin33ring}`

### External Writeups

* [https://github.com/HackThisCode/CTF-Writeups/blob/master/2017/EasyCTF/Mane%20Event/README.md](https://github.com/HackThisCode/CTF-Writeups/blob/master/2017/EasyCTF/Mane%20Event/README.md)
