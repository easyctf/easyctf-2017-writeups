# Genius - 230 points

Your boss told you that this team has come up with the cryptographic hash of the future, but something about their operation just seems a little fishy.

### Solution
###### Writeup by Valar Dragon
Here's the site:
![Screenshot 1](https://raw.githubusercontent.com/ValarDragon/CTF-Writeups/master/2017/EasyCTF/Genius/Site-1.png)
![Screenshot 2](https://raw.githubusercontent.com/ValarDragon/CTF-Writeups/master/2017/EasyCTF/Genius/Site-2.png)

So this tells us what we have to do, crack that hash, and make `$1000000000000000`!

Since they hinted at their hash being twice the strength of md5, I thought I'd just put it into [crackstation's](https://crackstation.net/) hash cracker, and see whats going on.

![Hash cracking](https://raw.githubusercontent.com/ValarDragon/CTF-Writeups/master/2017/EasyCTF/Genius/Crackstation.png)

This shows us that there are 4 byte words in these hashes!
Lets take the MD5 hash of `like`, and see how it compares to these strings.

`md5(like) = be1ab1632e4285edc3733b142935c60b`
The hash it was found in is
`be1ab1632e4285edc3733b142935c60b90383bad42309f7f6850d2b4250a713d0b2d7a97350465a02554d29d92bfefaf`

So the md5 of `like` is the first 32 chars of the line it was in! Each line of the hash is looking like it is the concatenation of 3 md5's of 4 byte strings!

So I split the given hash into 32 char chunks, and than began brute forcing 4 byte strings with lowercase and UPPERCASE letters, `{_}`, and numbers.
This gives us:

```
$ python3 geniusSolver.py
4cc801b880dddef59829a5ad08bd8a63   0_lo
d401ed3009d05ce4ef600d364a2c953e   0oO0
1292b9d4dc398866ef95869b22b3941e   3_md
78635bc95eaa7662a2ddf3e3d45cf108   5_we
8a7fca9234d2f19c8abfcd812971a26c   OMG_
90383bad42309f7f6850d2b4250a713d   _LIT
5cca214701dbe9f7f42da7bccf074b81   _MAK
ef843bee79633652a6d6ae08e964609f   _no_
0b2d7a97350465a02554d29d92bfefaf   eral
b42dad5453b2128a32f6612b13ea5d9f   have
00e883ab809346226dff6887080fb68b   id34
8c510dcaefd5061b191ad41d8b57d0ce   it_t
be1ab1632e4285edc3733b142935c60b   like
d64ddd0de1b187cd670783f5e28d681d   ly_s
73d559bc117f816333174e918d0587de   ng_2
631f5074f94b32730d0c025f1d7aacd7   ook_
4f4233d6c396e8a0e6fbf597d07b8817   rrk_
8d03f3f7757bdbdaaed60729d08bb180   you_
OMG_it_took_like_LITerally_s0oO00_long_2_MAK3_md5_werrk_you_have_no_id34
```

Putting that into the website's prompt gives a popup with the flag:
`easyctf{OUR_3nCRYpti0n_is_N0T_br0k3n_Ur_brok3n_6c5a390d}`

### Alternate Solution
###### Writeup by rofl0r
Here's the site:
![Screenshot 1](https://raw.githubusercontent.com/ValarDragon/CTF-Writeups/master/2017/EasyCTF/Genius/Site-1.png)
![Screenshot 2](https://raw.githubusercontent.com/ValarDragon/CTF-Writeups/master/2017/EasyCTF/Genius/Site-2.png)

the hash function was available as embedded javascript snippet in the page.
unrolled version:

```js
var super_secret_hash = function(s){
	function L(k,d){return(k<<d)|(k>>>(32-d))}
	function K(G,k){
		var I,d,F,H,x;
		F=(G&2147483648);
		H=(k&2147483648);
		I=(G&1073741824);
		d=(k&1073741824);
		x=(G&1073741823)+(k&1073741823);
		if(I&d){
			return(x^2147483648^F^H)
		}
		if(I|d){
			if(x&1073741824){
				return(x^3221225472^F^H)
			} else {
				return(x^1073741824^F^H)
			}
		} else	{
			return(x^F^H)
		}
	}
	function r(d,F,k){
		return(d&F)|((~d)&k)
	}
	function q(d,F,k){
		return(d&k)|(F&(~k))
	}
	function p(d,F,k){
		return(d^F^k)
	}
	function n(d,F,k){
		return(F^(d|(~k)))
	}
	function u(G,F,aa,Z,k,H,I){
		G=K(G,K(K(r(F,aa,Z),k),I));
		return K(L(G,H),F)
	}
	function f(G,F,aa,Z,k,H,I){
		G=K(G,K(K(q(F,aa,Z),k),I));
		return K(L(G,H),F)
	}
	function D(G,F,aa,Z,k,H,I){
		G=K(G,K(K(p(F,aa,Z),k),I));
		return K(L(G,H),F)
	}
	function t(G,F,aa,Z,k,H,I){
		G=K(G,K(K(n(F,aa,Z),k),I));
		return K(L(G,H),F)
	}
	function e(G){
		var Z;
		var F=G.length;
		var x=F+8;
		var k=(x-(x%64))/64;
		var I=(k+1)*16;
		var aa=Array(I-1);
		var d=0;
		var H=0;
		while(H<F){
			Z=(H-(H%4))/4;
			d=(H%4)*8;
			aa[Z]=(aa[Z]| (G.charCodeAt(H)<<d));
			H++
		}
		Z=(H-(H%4))/4;
		d=(H%4)*8;
		aa[Z]=aa[Z]|(128<<d);
		aa[I-2]=F<<3;
		aa[I-1]=F>>>29;
		return aa
	}
	function B(x){
		var k="",F="",G,d;
		for(d=0;d<=3;d++){
			G=(x>>>(d*8))&255;
			F="0"+G.toString(16);
			k=k+F.substr(F.length-2,2)
		}
		return k
	}
	function J(k){
		k=k.replace(/rn/g,"n");
		var d="";
		for(var F=0;F<k.length;F++){
			var x=k.charCodeAt(F);
			if(x<128){
				d+=String.fromCharCode(x)
			} else {
				if((x>127)&&(x<2048)){
					d+=String.fromCharCode((x>>6)|192);
					d+=String.fromCharCode((x&63)|128)
				} else {
					d+=String.fromCharCode((x>>12)|224);
					d+=String.fromCharCode(((x>>6)&63)|128);
					d+=String.fromCharCode((x&63)|128)
				}
			}
		}
		return d
	}
	var C=Array();
	var P,h,E,v,g,Y,X,W,V;
	var S=7,Q=12,N=17,M=22;
	var A=5,z=9,y=14,w=20;
	var o=4,m=11,l=16,j=23;
	var U=6,T=10,R=15,O=21;
	s=J(s);
	C=e(s);
	Y=1732584193;
	X=4023233417;
	W=2562383102;
	V=271733878;
	for(P=0;P<C.length;P+=16){
		h=Y;
		E=X;
		v=W;
		g=V;
		Y=u(Y,X,W,V,C[P+0],S,3614090360);
		V=u(V,Y,X,W,C[P+1],Q,3905402710);
		W=u(W,V,Y,X,C[P+2],N,606105819);
		X=u(X,W,V,Y,C[P+3],M,3250441966);
		Y=u(Y,X,W,V,C[P+4],S,4118548399);
		V=u(V,Y,X,W,C[P+5],Q,1200080426);
		W=u(W,V,Y,X,C[P+6],N,2821735955);
		X=u(X,W,V,Y,C[P+7],M,4249261313);
		Y=u(Y,X,W,V,C[P+8],S,1770035416);
		V=u(V,Y,X,W,C[P+9],Q,2336552879);
		W=u(W,V,Y,X,C[P+10],N,4294925233);
		X=u(X,W,V,Y,C[P+11],M,2304563134);
		Y=u(Y,X,W,V,C[P+12],S,1804603682);
		V=u(V,Y,X,W,C[P+13],Q,4254626195);
		W=u(W,V,Y,X,C[P+14],N,2792965006);
		X=u(X,W,V,Y,C[P+15],M,1236535329);
		Y=f(Y,X,W,V,C[P+1],A,4129170786);
		V=f(V,Y,X,W,C[P+6],z,3225465664);
		W=f(W,V,Y,X,C[P+11],y,643717713);
		X=f(X,W,V,Y,C[P+0],w,3921069994);
		Y=f(Y,X,W,V,C[P+5],A,3593408605);
		V=f(V,Y,X,W,C[P+10],z,38016083);
		W=f(W,V,Y,X,C[P+15],y,3634488961);
		X=f(X,W,V,Y,C[P+4],w,3889429448);
		Y=f(Y,X,W,V,C[P+9],A,568446438);
		V=f(V,Y,X,W,C[P+14],z,3275163606);
		W=f(W,V,Y,X,C[P+3],y,4107603335);
		X=f(X,W,V,Y,C[P+8],w,1163531501);
		Y=f(Y,X,W,V,C[P+13],A,2850285829);
		V=f(V,Y,X,W,C[P+2],z,4243563512);
		W=f(W,V,Y,X,C[P+7],y,1735328473);
		X=f(X,W,V,Y,C[P+12],w,2368359562);
		Y=D(Y,X,W,V,C[P+5],o,4294588738);
		V=D(V,Y,X,W,C[P+8],m,2272392833);
		W=D(W,V,Y,X,C[P+11],l,1839030562);
		X=D(X,W,V,Y,C[P+14],j,4259657740);
		Y=D(Y,X,W,V,C[P+1],o,2763975236);
		V=D(V,Y,X,W,C[P+4],m,1272893353);
		W=D(W,V,Y,X,C[P+7],l,4139469664);
		X=D(X,W,V,Y,C[P+10],j,3200236656);
		Y=D(Y,X,W,V,C[P+13],o,681279174);
		V=D(V,Y,X,W,C[P+0],m,3936430074);
		W=D(W,V,Y,X,C[P+3],l,3572445317);
		X=D(X,W,V,Y,C[P+6],j,76029189);
		Y=D(Y,X,W,V,C[P+9],o,3654602809);
		V=D(V,Y,X,W,C[P+12],m,3873151461);
		W=D(W,V,Y,X,C[P+15],l,530742520);
		X=D(X,W,V,Y,C[P+2],j,3299628645);
		Y=t(Y,X,W,V,C[P+0],U,4096336452);
		V=t(V,Y,X,W,C[P+7],T,1126891415);
		W=t(W,V,Y,X,C[P+14],R,2878612391);
		X=t(X,W,V,Y,C[P+5],O,4237533241);
		Y=t(Y,X,W,V,C[P+12],U,1700485571);
		V=t(V,Y,X,W,C[P+3],T,2399980690);
		W=t(W,V,Y,X,C[P+10],R,4293915773);
		X=t(X,W,V,Y,C[P+1],O,2240044497);
		Y=t(Y,X,W,V,C[P+8],U,1873313359);
		V=t(V,Y,X,W,C[P+15],T,4264355552);
		W=t(W,V,Y,X,C[P+6],R,2734768916);
		X=t(X,W,V,Y,C[P+13],O,1309151649);
		Y=t(Y,X,W,V,C[P+4],U,4149444226);
		V=t(V,Y,X,W,C[P+11],T,3174756917);
		W=t(W,V,Y,X,C[P+2],R,718787259);
		X=t(X,W,V,Y,C[P+9],O,3951481745);
		Y=K(Y,h);
		X=K(X,E);
		W=K(W,v);
		V=K(V,g)
	}
	var i=B(Y)+B(X)+B(W)+B(V);
	return i.toLowerCase()
}
;

function hash(word) {
	var out = "";
	for(var i = 0; i < word.length; i += 4) {
		out += super_secret_hash(word.substring(i, i + 4));
	}
	return out;
}

```
what immediately hit my eye is this statement:
```js
	for(var i = 0; i < word.length; i += 4) {
		out += super_secret_hash(word.substring(i, i + 4));
```
that makes it clear that the final hash is just a concatenation of hashes from the input string in chunks of 4 bytes.
so instead of analyzing the hash function, i just immediately started looking if it is feasible to bruteforce the hash.

for testing, i added the following code to generate a hash from a word passed in as a commandline argument:
```js
word = process.argv[2];  
console.log(hash(word)); 
```
and created the hash of the word "flag".
```
$ node genius-sum.js flag
327a6c4304ad5938eaf0efb6cc3e53dc
```
to generate the candidates for hashing, i used my battle-proofed bruteforce password generator (i call it brutus):
```c
#include <stdio.h>
#include <string.h>
#include <stdlib.h>

struct bruteforcer {
	unsigned long long step;
	char* out;
	size_t out_len;
	const char *alpha;
	size_t alpha_len;
	unsigned *pows;
};

static unsigned long upow(unsigned base, unsigned exp) {
	unsigned long res = 1, i=0;
	while(i++ < exp) res = res * base;
	return res;
}

static unsigned calc_pow(unsigned position, size_t maxlen, size_t alpha_len) {
	unsigned r = (maxlen - position) -1;
	return upow(alpha_len, r);
}

/* init bruteforcer state. out_len needs to be *not the buffersize*, but the size of characters in out!
   that means that you need to put the zero-terminator manually before initialising the state */
static void bruteforcer_init(struct bruteforcer *bf, char* out, size_t out_len, const char* alpha, size_t alpha_len) {
	*bf = (struct bruteforcer) {.step = -1ULL, .out = out, .out_len = out_len, .alpha = alpha, .alpha_len = alpha_len};
	bf->pows = malloc(sizeof *(bf->pows) * out_len);
	unsigned i;
	for(i=0; i< out_len; i++) bf->pows[i] = calc_pow(i, out_len, alpha_len);
}

static unsigned powstep(unsigned long long step, unsigned long long pwr, size_t alpha_len, int* termflag) {
	unsigned long long d = step / pwr;
	if(d < alpha_len) return d;
	else *termflag = 1;
	return 0;
}

static int bruteforcer_step(struct bruteforcer *b) {
	b->step++;
	unsigned long long mystep = b->step;
	size_t i;
	int termflag = 0;
	for(i=0;i < b->out_len; i++) {
		unsigned pwr = b->pows[i];
		unsigned x = powstep(mystep, pwr, b->alpha_len, &termflag);
		if(i == 0 && termflag) return 0;
		if(x) mystep -= (pwr * x);
		b->out[i] = b->alpha[ x ];
	}
	return 1;
}

static int usage(char *argv0) {
	printf(
	       "brutus 1.0 by rofl0r\n"
	       "usage: %s alphabet length\n"
	       "for example:\n"
	       "%s abcdef 4\n", argv0, argv0);
	return 1;
}

int main(int argc, char **argv) {
	if(argc != 3) return usage(argv[0]);
	const char *alpha = argv[1];
	struct bruteforcer b;
	int candidatelen = atoi(argv[2]);
	char* candidate = malloc(candidatelen+1);
	candidate[candidatelen] = 0;
	bruteforcer_init(&b, candidate, candidatelen, alpha, strlen(alpha));
	while(bruteforcer_step(&b)) puts(candidate);
	return 0;
}
```
Then i created a new js file with the hash function and the following snippet:
the idea is to pass the hash as command line argument (`want`), and the candidates to hash and compare via stdin.
```js
want = process.argv[2];

// that's a generic codeblock to be able to read from stdin.
var readline = require('readline');
var rl = readline.createInterface({
  input: process.stdin,
  output: process.stdout,
  terminal: false
});

rl.on('line', function(line){
  if (hash(line) === want) {
    console.log(line);
    process.exit(0);
  }
})
```

For the first try i used just a lowercase alphabet against the hash of "flag" to see if the attack is feasible (i.e. fast enough):

```
time ./brutus abcdefghijklmnopqrstuvwxyz 4 | node genius.js 327a6c4304ad5938eaf0efb6cc3e53dc
flag

real    0m1.087s
user    0m1.095s
sys     0m0.008s
```
very promising!

in order to use all 8 available cores, i resorted to my tool [jobflow](https://github.com/rofl0r/jobflow).
it's similar to GNU parallel, but it's much slimmer and faster since it's written in C.
and it uses much less memory. it's also by default installed on my distro [sabotage linux](http://github.com/sabotage-linux/sabotage).
the concept is to evenly distribute the input from brutus to 8 node.js processes running the "password cracker".
i then went to the guts and started cracking the actual hashes of the competition (in 32 byte chunks).

```
time ./brutus _-0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz 4 | jobflow -j=8 -exec node genius.js 8a7fca9234d2f19c8abfcd812971a26c
OMG_

real    0m24.328s
user    0m1.145s
sys     0m1.535s

```
success! the first part of the input string is `OMG_`.
since the lowercase alphabet didn't cut it, i expanded the alphabet for brutus to include uppercase, lowercase, plus some special chars.

i repeated the process with the other hash chunks, and got the final "password":
`OMG_it_took_like_LITerally_s0oO00_long_2_MAK3_md5_werrk_you_have_no_id34`

Putting that into the website's prompt gives a popup with the flag:
`easyctf{OUR_3nCRYpti0n_is_N0T_br0k3n_Ur_brok3n_6c5a390d}`

### External Writeups

* [https://github.com/HackThisCode/CTF-Writeups/blob/master/2017/EasyCTF/Genius/README.md](https://github.com/HackThisCode/CTF-Writeups/blob/master/2017/EasyCTF/Genius/README.md)
