# Flag PEG - 150 Points

We found a flag but it didn't do anything. Maybe you can find a better [flag](https://raw.githubusercontent.com/EasyCTF/easyctf-2017-problems/master/flag-peg/heresaflag.jpg)?

### Solution

###### Writeup by VoidMercy from phsst

We were given a .jpg file.

We ran binwalk on this file and obtained a zip containing a file named "KHgrbikqKC0xKV5u". This base64 decodes to: (x+n)*(-1)^n.

We were not really sure what this meant, but we do see that (-1)^n means something is alternating. We thought the bytes in the extracted file were alternating somehow and tested this idea. We see that the first byte is 89 in the extracted file. We immediately thought that this might be an encrypted PNG file. Looking up the file header and comparing it to the bytes in the beginning of the extracted file, we found that:

Starting from the first byte, and skipping every other byte, the difference between the PNG header and this is 0, 2, 4, etc..
Starting from the second byte, and skipping every other byte, the difference between the PNG header and this is 255, 253, 251, etc..

Now we can be fairly sure that the file was encrypted in this way. Here is my script:

```python
import struct

def bytes_from_file(filename):
    with open(filename, "rb") as f:
        while True:
            byte = f.read(1)
            if not byte:
                break
            yield(ord(byte))

n = 0
c = 0
tot = 155553
listofbytes = []
shit = ['0'] * tot
for b in bytes_from_file('KHgrbikqKC0xKV5u'):
    listofbytes.append(b)
print (listofbytes[0])
outFile = open("out", "wb")
while (c < 155553):
    byte = (listofbytes[c] - n) % 256
    shit[c] = byte
    c += 2
    n += 2
c = 1
n = 255
while (c < 155553):
    byte = (n - listofbytes[c]) % 256
    if (byte < 0):
        byte += 256
    shit[c] = byte
    c += 2
    n -= 2
for a in shit:
    outFile.write(chr(a))
print ("done")
outFile.close()
```

Here is the output:

![](https://raw.githubusercontent.com/VoidMercy/EasyCTF-Writeups-2017/master/forensics/Flag%20PEG/out.png)

## Flag

>easyctf{1_wr073_7h15_1n_m5p41n7}
