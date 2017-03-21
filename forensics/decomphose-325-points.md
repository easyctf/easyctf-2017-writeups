# Decomphose - 325 points

Image arithmetic is super neat until there's more than two images involved.

file 1

file 2

file 3

file 4

### Solution

To begin, I decompressed `file 1` and noticed that it contained images with seemingly random pixel noise:

![](https://github.com/hgarrereyn/EasyCTF-2017-Write-ups/raw/e3a3bd2cc09d92ad213624e51e4d6e5c85adfa05/decomphose/207188208176.png)

Upon closer inspection, some pixels seem to be surrounded on all sides by black pixels:

![](https://github.com/hgarrereyn/EasyCTF-2017-Write-ups/raw/e3a3bd2cc09d92ad213624e51e4d6e5c85adfa05/decomphose/209123584873_zoom.png)

The other three folders contained similar images.

Perhaps if I could isolate those bordered pixels from each image and overlay them, I could find some clue.

I opened the first photo in GIMP (a free, open-source photo editing application).

![](https://github.com/hgarrereyn/EasyCTF-2017-Write-ups/raw/e3a3bd2cc09d92ad213624e51e4d6e5c85adfa05/decomphose/0_gimp.png)

After a bit of mucking around trying to find a way to isolate those pixels, I figured out a method:

#### 1. Set black pixels transparent

Do `Layers > Transparency > Color to Alpha...` and set the color selection to `#000000`:

![](https://github.com/hgarrereyn/EasyCTF-2017-Write-ups/raw/e3a3bd2cc09d92ad213624e51e4d6e5c85adfa05/decomphose/1_gimp.png)

I obtained this:

![](https://github.com/hgarrereyn/EasyCTF-2017-Write-ups/raw/e3a3bd2cc09d92ad213624e51e4d6e5c85adfa05/decomphose/2_gimp.png)

#### 2. Fill noisy pixels

Select the paint-bucket tool and set the threshold to `200` and the color to `#000000`.

Then click on any of the noisy pixels and they will all turn black. (the paint bucket won't cross alpha borders)

![](https://github.com/hgarrereyn/EasyCTF-2017-Write-ups/raw/e3a3bd2cc09d92ad213624e51e4d6e5c85adfa05/decomphose/3_gimp.png)

#### 3. Remove noisy pixels

Now we can simply repeat step 1 again to remove the noisy pixels. (There are a few artifacts but it won't matter)

![](https://github.com/hgarrereyn/EasyCTF-2017-Write-ups/raw/e3a3bd2cc09d92ad213624e51e4d6e5c85adfa05/decomphose/4_gimp.png)

#### Combine all the images

In order to combine all the images, I simply opened the images as layers in GIMP and repeated the process above for each layer.

*Note: a handy shortcut is CMD-F or CTRL-F to repeat the last filter (eg. Color to Alpha)*

My final image looked like this:

![](https://github.com/hgarrereyn/EasyCTF-2017-Write-ups/raw/e3a3bd2cc09d92ad213624e51e4d6e5c85adfa05/decomphose/decomphose.png)

And zooming in on the flag, we obtain:

![](https://github.com/hgarrereyn/EasyCTF-2017-Write-ups/raw/e3a3bd2cc09d92ad213624e51e4d6e5c85adfa05/decomphose/decomphose_flag.png)

`easyctf{wh4t_a_5weet_fFLag_2b04e1}`

### External Writeups

* \(none\)
