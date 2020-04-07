---
title: "Generators in Python: \"good lazy\""
date: 2020-02-01
draft: false
tags: ["programming", "technology"]
---
# Introduction
Generators are functions that behave like iterators. They allow us to evaluate values only as we need them ("lazily evaluate", or "lazy evaluation").
# A basic generator
Your basic generator has the following structure:
```Python
def generator_name(args...):
    ...
    while <we can still generate values>:
        yield <next value>
        ...
```
Let's look at a basic generator:
```Python
def jumping_integers(start, end, jump=1):
    """Starting at given value, return jumped integers until end, exclusive."""
    integer = start
    while integer < end:
        yield integer
        integer += jump
```
Attentive programmers will notice this looks a lot like `range`, particularly `xrange` in Python 2 and `range` in Python 3. Indeed, if we test the generator we just wrote:
```Python
if __name__ == "__main__":
    for integer in jumping_integers(0, 12):
        print(f"I got an integer with value {integer}!")
```
we get the following output:
```
I got an integer with value 0!
I got an integer with value 1!
I got an integer with value 2!
I got an integer with value 3!
I got an integer with value 4!
I got an integer with value 5!
I got an integer with value 6!
I got an integer with value 7!
I got an integer with value 8!
I got an integer with value 9!
I got an integer with value 10!
I got an integer with value 11!
```
# Benefits of generators
The main benefits of generators (i.e. lazy evaluation) are:
1. They allow us to keep only the values we need around.
2. They allow us to process values as we generate them (without waiting for all values).

Let's go back to the previous example to see these benefits.

First, let's replace 12 with a very large end of range like 1e8 (100 million), and write to file so we don't flood the screen with print statements:
```Python
if __name__ == "__main__":
    first_write = True
    with open("output.txt", "w") as output:
        for integer in jumping_integers(0, 1e8):
            output.write(f"integer with value {integer}\n")

            if first_write:
                print("started writing output")
                first_write = False
```
The program takes roughly a minute to finish on my laptop, and according to the new print statement, starts writing to the output file immediately.

What if we constructed a list instead?
```Python
if __name__ == "__main__":
    first_write = True
    with open("output.txt", "w") as output:
        integers = list(jumping_integers(0, 1e8))
        for integer in integers:
            output.write(f"integer with value {integer}\n")

            if first_write:
                print("started writing output")
                first_write = False
```
The program still takes roughly a minute to finish. However, according to the print statement, we only start writing about 10 seconds in. More frustratingly, while running the new program, my laptop starts to slow down, including freezing the screen. If there was a burden on my laptop with the generator version, it seems to have worsened with the list construction version.

Let's look at why the first program has the aforementioned benefits using [the `time` command. Note the `time` command is not the same as the `time` builtin](https://unix.stackexchange.com/questions/375889/unix-command-to-tell-how-much-ram-was-used-during-program-runtime).

When we run
```
/usr/bin/time -f "%E total run time, %M max memory" python <first version with generator>.py
```
the output is
```
0:57.04 total run time, 8712 max memory
```

When we run
```
/usr/bin/time -f "%E total run time, %M max memory" python <second version with list construction>.py
```
the output is
```
0:58.52 total run time, 3965404 max memory
```

As we might intuit, the second version with list construction suffers because it constructs the entire list of 100 million numbers in memory before it starts writing to the output file. While there may be some caching benefit, we certainly consume significantly more memory and create significantly more latency with the second version.
# Conclusion
Generators are useful when we expect to spend a lot of time and/or memory to generate values we want to iterate over to process. They allow us to take advantage of lazy evaluation to reduce memory usage and processing latency.
