# 20xx - 50 points

My friend sent me [this file](https://github.com/EasyCTF/easyctf-2017-problems/blob/master/20xx/_20xx.dtm?raw=true) and told me to git gud.

### Solution
##### Writeup by Alaska47 from phsst

Searching the dtm extension up originally lead me towards AutoCAD software. However, once we look at the title, we realize that this relates to the Super Smash Bros. Melee game. Searching up `dtm game file` leads us to this [link](http://tasvideos.org/DTM.html).

Now that we know this is a Dolphin gameplay file, I downloaded Dolphin and started looking for an ISO we can use to open the dtm file. Searching up Super Smash Bros. along with the Game ID: `GALE01`, reveals that there are 3 different versions of the game. I tried each one (opening the ISO through Dolphin and then going to `Movie -> Play Input Recording`) and finally version 1.0.2 was the one that matched the checksum stored in the dtm file. 

![](https://github.com/VoidMercy/EasyCTF-Writeups-2017/blob/master/forensics/20xx/checksum.png?raw=true)

After we started playing the dtm file, we come across a little problem. A significant amount of the dtm is spent in the beginning where we see the No Mem. Card notification. If you listen to the audio, you can hear that there are clicking noises and the cursor is moving around. I knew that in Smash, there's a character select screen where you move your cursor around and pick players. This got me thinking if there was any way I could open the game straight to the character selection screen. 

After some research, I came across this [link](https://smashboards.com/threads/melee-gecko-codes-guide-and-discussion.327311/). Scrolling down, we can see there's a Gecko Cheat to boot straight to character select. Unfortunately for me, this cheat didn't work for some reason. After reading up on Smash Melee some more, I realized that another way to boot straight to character select is by choosing to enable Netplay Community Settings.

![](https://github.com/VoidMercy/EasyCTF-Writeups-2017/blob/master/forensics/20xx/cheat.png?raw=true)

Now we can run the dtm file and go straight to character select. The cursor moves around and selects players multiple times. I wrote down which character and the number of times they were clicked on. After staring at this for a while, I tried taking the nth letter of character based on how many times they were clicked on. Doing this reveals our flag.

![](https://github.com/VoidMercy/EasyCTF-Writeups-2017/blob/master/forensics/20xx/fin.png?raw=true)

## Flag
>easyctf{foxonlyfd}
