# Hex QR - 200 points

We were given a mysterious hexagonal qr code and a website that could produce a similar looking qr code for a given string.

The goal was to figure out the encoding schema and decode the flag.

***The flag to decode:***

![](https://github.com/hgarrereyn/EasyCTF-2017-Write-ups/raw/1e81630e6b36e9caaa54b23e63e4d1e4f6f5c39e/hexqr/flag.png)

### Solution:

#### Overview

In order to get the flag, we don't have to completely understand the encoding schema, we just have to understand it *well enough* to decode a message.

We had access to a web page that would encode any string. After a bit of probing, I discovered that I could enter any ascii character (7 bits) but unicode characters caused the program to throw an error. So it must only be using 7-bits.

#### Single character

I decided to test a bunch of single character strings and look for patterns. Here are the generated codes for the following characters:

#### `A - 0x41 - 1000001`
![A](https://github.com/hgarrereyn/EasyCTF-2017-Write-ups/raw/1e81630e6b36e9caaa54b23e63e4d1e4f6f5c39e/hexqr/A.jpeg)

#### `B - 0x42 - 1000010`
![B](https://github.com/hgarrereyn/EasyCTF-2017-Write-ups/raw/1e81630e6b36e9caaa54b23e63e4d1e4f6f5c39e/hexqr/B.jpeg)

#### `C - 0x43 - 1000011`
![C](https://github.com/hgarrereyn/EasyCTF-2017-Write-ups/raw/1e81630e6b36e9caaa54b23e63e4d1e4f6f5c39e/hexqr/C.jpeg)

Eventually, I discovered that for all single character strings, only the following 6 bits seemed to change:

![](https://github.com/hgarrereyn/EasyCTF-2017-Write-ups/raw/1e81630e6b36e9caaa54b23e63e4d1e4f6f5c39e/hexqr/1-char.png)

How could 7-bit ascii be encoded in only 6 bits? To see what bit was left out, I tried comparing two characters with equal bits except for one. What I discovered is that the most-significant bit was being left out. For example, the image produced for the following characters was identical:

```
0: 0110000
p: 1110000
```

*Later I discovered that this is not exactly the case... but it works for now*

#### Mapping the bits

Now I wanted to figure out which bit corresponded with each triangle. Here is the syntax I will use:

```
bit | 6 5 4 3 2 1 0
-------------------
A   | 1 0 0 0 0 0 1
B   | 1 0 0 0 0 1 0
C   | 1 0 0 0 0 1 1
...
```

Through some more detective work, I discovered that for all ascii characters with bit 6 set, the triangles corresponded as follows:

![](https://github.com/hgarrereyn/EasyCTF-2017-Write-ups/raw/1e81630e6b36e9caaa54b23e63e4d1e4f6f5c39e/hexqr/1-char-labeled.png)

Now for characters without bit 6 set, they appeared to be shifted left and padded with a zero. For example `4` is encoded as follows:

```
4 (normal)  | 0 1 1 0 1 0 0
4 (encoded) | 1 1 0 1 0 0 0
which appears the same as
h           | 1 1 0 1 0 0 0
```

*Note: Working with only one character caused me to stumble onto a weird edge case where the code was shifted depending on whether the 6th bit of the first character was set or not - we don't have to worry about that for now.*

#### Multiple characters

One obvious difference between one and two character strings is that the qr code gets bigger. Instead of three base triangles, we now have 4:

![](https://github.com/hgarrereyn/EasyCTF-2017-Write-ups/raw/1e81630e6b36e9caaa54b23e63e4d1e4f6f5c39e/hexqr/bigger.png)

Another difference is that the qr code seems to flip horizontally based on the number of characters.

I repeated the same procedure of mapping as before and obtained the following character positions (I only used characters with bit 6 set without realizing it -> this will be important later):

#### Two characters
*I've omitted bits `1-4` but they are in order*
![](https://github.com/hgarrereyn/EasyCTF-2017-Write-ups/raw/1e81630e6b36e9caaa54b23e63e4d1e4f6f5c39e/hexqr/2-char-labeled.png)

#### Three characters
![](https://github.com/hgarrereyn/EasyCTF-2017-Write-ups/raw/1e81630e6b36e9caaa54b23e63e4d1e4f6f5c39e/hexqr/3-char-labeled.png)

Now the method of reading these codes is becoming clear: They are read in a zig-zag pattern starting from the top. For example to read a three character code, use the following path:

![](https://github.com/hgarrereyn/EasyCTF-2017-Write-ups/raw/1e81630e6b36e9caaa54b23e63e4d1e4f6f5c39e/hexqr/read-pattern.png)

Then you obtain binary like this:

```
010 [char 1] 00 [char 2] 00 [char 3] ...
```

At some point during testing, I encountered a binary string like this:

```
... [char n] 01 [char n+1] ...
             ^^
			 notice
```

This confused me for a moment and caused me to revisit my original idea. Why would the spacing bits be different?

After some more testing, I realized that for every character after the first one, the two spacing bits corresponded to bits `6` and `7`. I rechecked a few things and discovered the correct encoding.

To read these qr codes, follow the zigzag pattern above and then perform a `change, keep, change, keep...` operation on the resulting binary string.

In more mathematical terms, you take the binary string and `XOR` with a string of `1010101010...`

#### The correct encoding

*Note this is the format after you apply the `XOR` as described above*

```
 binary     notes
--------   -------
11         - header (if this is 00, you are reading it wrong
	         and should flip horizontally)

[char 1]   - if bit 6 is set, this is bits 6 -> 0 of char 1,
             otherwise it is bits 5 -> 0 of char 1

[char 2]   - the remaining chars are all bits 7 -> 0
[char 3]     (bit 7 is always 0 because it is ascii)
[char 4]
...

00000000   - once you hit zeros, you've reached the end
```

In graphical form, the encoding is as follows:

***Bit 6 is set***

*`b1` indicates a binary 1 value*

![](https://github.com/hgarrereyn/EasyCTF-2017-Write-ups/raw/1e81630e6b36e9caaa54b23e63e4d1e4f6f5c39e/hexqr/3-char-correct.png)

***Bit 6 is not set***

![](https://github.com/hgarrereyn/EasyCTF-2017-Write-ups/raw/1e81630e6b36e9caaa54b23e63e4d1e4f6f5c39e/hexqr/3-char-correct-nob6.png)

*Notice that if bit 6 is not set, the entire rest of the code is shifted one bit*

#### Decoding the flag

Now that we know the encoding schema, it is trivial to read the binary and decode the flag. I read the triangles by hand and then fed it into a python program to perform `XOR` and convert to ascii.

Here is the program I wrote (it's a little rough):

```python
# Reading the qr code directly
hex_raw = """
0101100000011010000
100110001011000011011
00010000100110011001011
1000110100001001110011000
000001010001000010010011100
11110000110100001110110011001
0001110010011000000100110000010
100011100000111010001001110011000
00000101000100110001100000011011000
10000000100111001100000000101000100
001001111010011010000111011000010
1000100110001001000010000000110
10000100111001100000010011001
101010000010100011011001100
0000110000100110011001101
10001100010011000000110
111001010000101010101
0101010101010101010
""".replace('\n','')

# convert the hex_code to a binary value
hex_raw_value = int(hex_raw,2)

# create a string of the same length to xor with
xor_value = int(('10'*(len(hex_raw)/2))[0:len(hex_raw)],2)

# perform XOR and convert back to binary string
hex_code = str(bin(hex_raw_value ^ xor_value))[2:]

out_string = ""
i = 2 # skip the 2 bit header

# add the first character (guessing that bit 6 is set)
bit6IsSet = True
out_string = out_string + chr(int(hex_code[i:i + (7 if bit6IsSet else 6)], 2))
i = i + (7 if bit6IsSet else 6)

# the rest of the characters are 8 bit
while i < len(hex_code)-8:
	out_string = out_string + chr(int(hex_code[i:i+8], 2))
	i = i + 8

print(out_string)
```

The output was `easyctf{are_triangles_more_secure_than_squares?_c54fcdeb}`

#### Bonus

On twitter, @easyctf posted this image as a teaser:

![](https://github.com/hgarrereyn/EasyCTF-2017-Write-ups/raw/1e81630e6b36e9caaa54b23e63e4d1e4f6f5c39e/hexqr/bonus.jpg)

Which decodes as `i <3 cheesecake`

##### Writeup by Harrison Green

### External Writeups

* \(none\)
