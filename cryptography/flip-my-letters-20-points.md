# Flip My Letters - 20 points

I dropped my alphabet on its head, can you help me reassemble it?
```
easyctf{r_wlmg_vevm_mvvw_zm_zhxrr_gzyov}
```

### Solution

The hint suggests that it's a substitution cipher, where the key is the alphabet in reverse.
It is easy to test the idea using the [tr](https://en.wikipedia.org/wiki/Tr_%28Unix%29) Unix command.
Sure enough, it works.

```
$ echo "r_wlmg_vevm_mvvw_zm_zhxrr_gzyov" | tr abcdefghijklmnopqrstuvwxyz zyxwvutsrqponmlkjihgfedcba
i_dont_even_need_an_ascii_table
```

Flag:
```
easyctf{i_dont_even_need_an_ascii_table}
```

### External Writeups
