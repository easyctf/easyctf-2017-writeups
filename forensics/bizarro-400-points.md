# Bizarro - 400 Points

Something seems very strange about [this](https://raw.githubusercontent.com/EasyCTF/easyctf-2017-problems/master/bizarro/crpt.png) strange looking image. Check it out?

### Solution

###### Writeup by VoidMercy from phsst

We were given an image.

We put the image into stegsolve and in the red plain, we can see a qr code. We decoded the QR code and got:

```
CI XCVII CXV CXXI XCIX CXVI CII CXXIII CXVI CIV CV CXV XCV CV CXV XCV CX CXI CXVI XCV CXVI CIV CI XCV CII CVIII XCVII CIII CXXV XXXII CV XXXII CVII CX CXI CXIX XXXII CXVI CIV CV CXV XXXII CII CVIII XCVII CIII XXXII CV CXV XXXII CXIX CI CV CXIV C XXXII CV XXXII CVI CXVII CXV CXVI XXXII XCIX XCVII CX XXXIX CXVI XXXII CXII CXVII CXVI XXXII CIX CXXI XXXII CII CV CX CIII CI CXIV XXXII CXI CX XXXII CXIX CIV CXXI
```

Then we translated the roman numerals to numbers, and converted that to ascii and got:

easyctf{this_is_not_the_flag} i know this flag is weird i just cant put my finger on why

So we got a red herring. Then, looking at the hint: "Red Herrings are always a _touchy_ subject", and "take a blind guess" and "stumble," we surmised that this problem is somewhat related to braille.

We then saw that the error correction level of this QR code is H and the mask is 6. Since the error correction level is high, there might be differences. We generated a QR with the same text, mask, and error correction level, then compared the two. We see that there are differences. 

![](https://github.com/VoidMercy/EasyCTF-Writeups-2017/blob/master/forensics/bizarro/diff.png)

I cropped out this 89x11 section of the original qr code, and then wrote a script to decode braille on this qr code. Here is my script to extract the binary of the file, then decode it from braille

```python
from PIL import Image

def get(x, y, pic):
    c = pic.getpixel((x, y))
    if (c == (0, 0, 0)):
        return '1'
    return '0'


pic = Image.open("qr1.png").convert("RGB")
width, height = pic.size
total = ""
for i in range(height):
    gu = ""
    for a in range(width):
        gu += get(a, i, pic)
        total += get(a, i, pic)
    print(gu)
print ("done")
print (total)
```

```python
data = "0000011010110011001101010001100101110100100011000000100100010101101111001011101001001000000111100111001001101010001000100110001101001101001001100111101011001001001110001010101000001111000110000110101001001000000110101101111100000111101110001001101110101001001101101010001011101000001100001001000111101001111001001011101001101000111100001011001101111010001011101000000110001010101010100111101011001011101001001001001110001000001000101001001111101110001001101000111011101100001001111001001011101001101101000111101011001011101001001001000110000110100110101001111001001011101001101001111001001011101001101110001000001000001000001000001000101001001000111101101101001001001011100110101100001001101011101001001001001110001100001001001001001000001100001000001100001010101001100110101001001010001011101000001100001001001100101000001000101001000110101000111011101001000111101001101100001011001000001101101111001001000111101011001001001101101000111100101010001001001011100110101011000000100001"
print (data)

braille = {'100000' : 'a', '101000' : 'b', '110000' : 'c', '110100' : 'd', '100100' : 'e', '111000' : 'f', '111100' : 'g', '101100' : 'h', '011000' : 'i', '011100' : 'j', '100010' : 'k', '101010' : 'l', '110010' : 'm', '110110' : 'n', '100110' : 'o', '111010' : 'p', '111110' : 'q', '101110' : 'r', '011010' : 's', '011110' : 't', '100011' : 'u', '101011' : 'v', '011101' : 'w', '110011' : 'x', '110111' : 'y', '100111' : 'z', '010111' : '#'}


ans = ""
a = 0
while (a < len(data)):
    if (a + 5 < len(data)):
        #temp = data[a + 1] + data[a + 3] + data[a + 5] + data[a + 0] + data[a + 2] + data[a + 4]
        temp = data[a + 0] + data[a + 1] + data[a + 2] + data[a + 3] + data[a + 4] + data[a + 5]
        #temp = data[a + 0] + data[a + 2] + data[a + 4] + data[a + 1] + data[a + 3] + data[a + 5]
        #temp = data[a + 1] + data[a + 3] + data[a + 5] + data[a + 0] + data[a + 2] + data[a + 4]
        #temp.replace("0", "a").replace("1", "0").replace("a", "1")
        print (temp)
        try:
            print (braille[temp])
            ans += braille[temp]
        except:
            ans += "|"
    a += 6
print (ans)
```

From this we get:
easyctfopenbracetzerouchybraillthreefakeqfourczerodthreeisszerozerofaaaakeunderscoreefceeacaclosebracemakesuretochangethenumbers
Translating this, we get the flag.

## Flag

>easyctf{t0uchybraill3fakeq4c0d3iss00faaaake_efceeaca}
