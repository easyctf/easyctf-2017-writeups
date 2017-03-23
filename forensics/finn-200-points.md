# Finn - 200 points

The Resistance intercepted this suspicious [picture](https://github.com/EasyCTF/easyctf-2017-problems/blob/master/finn/finn.jpg?raw=true) of Finn's old stormtrooper helmet, sent by General Hux to Kylo Ren. Hux isn't exactly Finn's biggest fan. What could he be hiding? Good luck!

If you get stuck, We also have [this](https://github.com/EasyCTF/easyctf-2017-problems/blob/master/finn/help.txt?raw=true) blob of sarcasm, which may or may not be useful in your quest. Worth a shot right?.

### Solution
##### Writeup by Alaska47 from phsst

We are given a jpg. Looking at the first part of the hint, we know that we have to `binwalk` the file. Running `binwalk -e finn.jpg` gives us what we need.

After we binwalk it, we see that there is a zip file protected by a password. Although the hint says that the password refers to the problem statement, I just used brute force using the `fcrackzip` tool. Running `fcrackzip -v -m zip2 -l 1-8 -u AD3E.zip` gives us the password to the zip file: `2187`.

After extracting the zip file, we see two "identical" images. The hint says we should find the difference in the images. Imagemagick's `compare` tool should do the trick. Running `compare kylo1.png kylo2.png diff.png` gives us a new file which contains a QR code. 

![](https://github.com/VoidMercy/EasyCTF-Writeups-2017/blob/master/forensics/Finn/small_diff.png?raw=true)

Scanning the QR code using any online scanner gives us some text:

`\x63\x68\x66\x63\x7e\x71\x73\x34\x76\x57\x72\x3c\x74\x73\x5c\x31\x75\x5d\x6b\x32\x34\x77\x59\x38\x4c\x7f`

The next part of the hint tells us to look more closely at the difference in the two images. After manually comparing the differences in the pixels, it looks like some of the pixels diffeerentiate by more than 1. Starting at (144, 533) in the image, we find a string of pixels which have differences ranging from 5-8 for about ~30 pixels in the x-direction. 

![](https://github.com/VoidMercy/EasyCTF-Writeups-2017/blob/master/forensics/Finn/subs.png?raw=true)

After seeing the hex bytes extracted from the QR code, I started to think that the encryption refered to by the hint was XOR encryption. This meant that the message and the key needed to have the same size. Starting at (144, 533), I extracted the differences in the next 26 pixels.

```python
from PIL import Image

f = Image.open("kylo1.png").convert("RGB")
g = Image.open("kylo2.png").convert("RGB")

diffs = []

for x in range(f.size[0]):
    for y in range(f.size[1]):
        pix1 = f.getpixel((x,y))
        pix2 = g.getpixel((x,y))
        sub = (abs(pix2[0]-pix1[0]),abs(pix2[1]-pix1[1]),abs(pix2[2]-pix1[2]))
        if(sub != (0,0,0) and sub != (1,1,1)):
            print((x,y))
            diffs.append(sub[0])
print(diffs)

gu = []
for x in range(144, 144+26):
    pix1 = f.getpixel((x,533))
    pix2 = g.getpixel((x,533))
    gg = abs(pix2[0]-pix1[0])
    gu.append(str(gg))
print("".join(gu))
```

This script outputs `54745270485860306291136282`.

Armed with a message and the key, I simply used an online XOR tool to XOR `636866637e7173347657723c74735c31755d6b32347759384c7f` and `0504070405020700040805080600030006020901010306020802`. Doing so gives us another hex string, `666c61677b737434725f773472735f31735f623335745f3a447d`, which can be converted to text to reveal the flag.

## Flag
>easyctf{st4r_w4rs_1s_b35t_:D}
