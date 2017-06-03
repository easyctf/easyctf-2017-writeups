# Fzz Buzz 2 - 200 points

Oh no! Two of my keys are broken! Please help me make the same Fzz Buzz program, sans that one letter and queston marks.

As a side note, use of `eval()` and `exec()` is also frowned upon and will be marked invalid.

### Solution

***This writeup is a python solution***

The goal of this challenge was to write a fizz buzz program without using the letter `i` or the question mark character `?`. You must read an integer `n` from stdin and print that many lines of fizz buzz. For example, if `n = 17`, output:

```
1
2
Fizz
4
Buzz
Fizz
7
8
Fizz
Buzz
11
Fizz
13
14
FizzBuzz
16
17
```

#### How to get input / print

In order to get user input, you must call `input()` and likewise, to print you must call `print()`. Therefore, we need some way to alias these functions without using the character `i`.

**Solution:**

Both `input` and `print` belong to the `__builtins__` module that is loaded automatically on startup. Therefore, we can find a reference to these functions by using `getattr` on the `__builtins__` module.

```python
f = getattr(globals()['__builtins__'],'input')
p = getattr(globals()['__builtins__'],'print')
```

Then by simply escaping the `i` character we get this:

```python
f = getattr(globals()['__bu\x69lt\x69ns__'],'\x69nput')
p = getattr(globals()['__bu\x69lt\x69ns__'],'pr\x69nt')
```

Now `f` will call `input` and `p` will call `print`.

#### How to loop

Since the user provides the number of lines to print, we must find some way to loop from `1` to `n`.

**Solution:**

Let's use recursion. We will read `n` from the input and then define a function `go(k)` that prints the text for line `k` and calls itself with `k+1` if `k < n`. Finally, we will start it off by calling `go(1)` That looks like this:

```python
n = f() # Using our alias

def go(k):
	# Do some print magic here
	if (k < n):
		go(k+1)

go(1) # Start the chain
```

#### How to use conditionals?

We ran into a problem in our last task: we can't use `if` since it has an `i`. Therefore, we must find some other way to get conditional behavior.

**Solution:**

How about short-circuiting? In python (and many other languages) a conditional will stop evaluation if one side can determine the entire expression. For example, in the following expression, the conditional inside `print` checks if `a == 1`. Since that is `False`, there is no way for the expression to be true (because `False AND x == False` regardless of `x`) Therefore, the expression `b == 1` isn't even evaluated.

```python
a = 5
b = 3

print(a == 1 and b == 1)
```

We can use this to our advantage. How about if we put the check as the first conditional and the print as our second. Therefore, if the check succeeds, we print the line. Here is what that looks like:

```python
a = ((k % 15 == 0) and p('F\x69zzBuzz'))
```

In the previous line, we first check if `k` (the line number) is divisible by `3` and `5` (or just `15`). If it is not, the conditional short-circuits and the right side is not evaluated. If it is, we call `p('F\x69zzBuzz')` which prints `FizzBuzz`. (The `a =` is just so that it is a valid expression).

#### Putting it all together

Therefore, using all of these ideas, we can come up with the following program:

```python
# Create aliases
f = getattr(globals()['__bu\x69lt\x69ns__'],'\x69nput')
p = getattr(globals()['__bu\x69lt\x69ns__'],'pr\x69nt')

# Get user input
n = f()

# Prints text for line k and calls itself with the next line
def go(k):
	a = ((k % 15 == 0) and p('F\x69zzBuzz'))
	a = ((k % 3 != 0 and k % 5 == 0) and p('Buzz'))
	a = ((k % 3 == 0 and k % 5 != 0) and p('F\x69zz'))
	a = ((k % 3 != 0 and k % 5 != 0) and p(k))
	a = ((k < n) and go(k + 1))

go(1)
```

##### Writeup by Harrison Green

### External Writeups

* [https://github.com/andrewjbennett/ctf-writeups/blob/master/2017/easyctf/fzz-buzz-2-200-points.md](https://github.com/andrewjbennett/ctf-writeups/blob/master/2017/easyctf/fzz-buzz-2-200-points.md)
