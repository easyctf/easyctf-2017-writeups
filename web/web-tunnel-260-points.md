# Web Tunnel - 260  Points

I was just going to search some random cat videos on a Saturday morning when my friend came up to me and told me to reach the end of this tunnel. Can you do it for me? - http://tunnel.web.easyctf.com

### Solution

Going to the site we are given a QR code. Scanning the QR code gives a random string of length 20. Redirecting the browser to this string, another QR code appears, so on and so forth.

We are going to need an automated way to scan these QR codes, store the string, and download the next QR code. Over and over.

We will be using the zbarimg library to scan the QR codes. You can get this libaray by typing: ```sudo apt-get install zbar-tools```. Now coding a bash script to automate the proccess.

```shell
#!/bin/bash

# Starting image ID
nextImg="DaicO7460493nYSuvLPW"

# Create an aoutput file for analysis
touch out.txt

# Loop for a long time
while true
do
	wget http://tunnel.web.easyctf.com/images/$nextImg
	raw=`zbarimg $nextImg`
	nextImg=${raw:8:20}
    echo $raw > out.txt
done
```
Running the program for a few minutes and searching the output file for 'easyctf; gets us the flag.


###### Flag: easyctf{y0u_sh0uld_b3_t1r3d_tr4v3ll1ng_all_th1s_w4y}

### External Writeups

* [https://github.com/HackThisCode/CTF-Writeups/blob/master/2017/EasyCTF/Web%20Tunnel/README.md](https://github.com/HackThisCode/CTF-Writeups/blob/master/2017/EasyCTF/Web%20Tunnel/README.md)
