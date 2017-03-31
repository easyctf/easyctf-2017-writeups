# My USB - 150 points

I found_ [my usb](https://github.com/EasyCTF/easyctf-2017-problems/blob/master/my-usb/usb.img) from a long time ago. I know there's a flag on there somewhere; can you help me find it?

### Solution
###### Writeup by Valar Dragon

We're given a USB img. First thing I did was try to actually restore the USB image onto a USB. We see a document called "hack.docx", with two images in it, and a zip file called flag.zip, with an image of the usaflag in it, and an image called cryptolock.png.

Doing my standard forensic analysis, like looking at hexdumps, and looking for other file headers through scalpel, yielded nothing.
There is some code that is visible on both of the images inside the word file, but it seems too distorted to be relevant.

Then I thought to scalpel the original USB img file.

``` bash
$ scalpel -c scalpelConfig.txt  usb.img
```

This gives 3 images, instead of just the two inside of the docx! (It doesn't give the image inside of flag.zip)

Heres the extra file:

![flag.jpg](https://raw.githubusercontent.com/HackThisCode/CTF-Writeups/master/2017/EasyCTF/My%20USB/flag.jpg)

Theres our flag!
`flag{d3let3d_f1l3z_r_k00l}`

### External Writeups

* [https://github.com/HackThisCode/CTF-Writeups/blob/master/2017/EasyCTF/My%20USB/README.md](https://github.com/HackThisCode/CTF-Writeups/blob/master/2017/EasyCTF/My%20USB/README.md)
