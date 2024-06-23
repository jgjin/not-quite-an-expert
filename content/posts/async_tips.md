---
title: "async Python tips"
date: 2020-08-22
draft: false
tags: ["technology"]
---
# Introduction
When you live on the edge, you get cut! `async` came to Python rather recently, so I've been "cut" using it quite a few times. If you want to get started with `async` Python, I have a few tips:
1. `await` every `async`, except one.
2. `async` can include non-`async`. Non-`async` cannot include `async`.
3. You cannot interchange `async` and non-`async` constructs.
# Tip 1
An `async def` function actually returns something known as a coroutine. To run a coroutine, you must either:
1. `await` it
2. run it through an event loop
3. wrap it in a [Task](https://docs.python.org/3/library/asyncio-task.html#asyncio.Task)

Let's ignore the third option in favor of simplicity. When getting used to `async` Python, make the code work before making it fast.

You can only use the second option once. Otherwise the program will complain at run time. Therefore, you should use it on the main `async` function, i.e. the entry point of all other `async` code. You can use `asyncio.run(main_async())` or `asyncio.get_event_loop().run_until_complete(main_async())`, whichever you prefer. The rest, as mentioned before, should come in the form `await func_async()`.
# Tip 2
The following code works:
```Python
async def func_async():
    for elt in ...
```

The following does **NOT** work:
```Python
def func_sync():
    async for elt in ...
```

You cannot call `await`, `async with`, nor `async for` inside of non-`async` functions/blocks. `async` code will generally only appear in a non-`async` function/block once in your program, when calling `asyncio.run(main_async())` or `asyncio.get_event_loop().run_until_complete(main_async())` from the second option of Tip 1.
# Tip 3
Under the hood, `async` constructs rely on different functions than their non-`async` counterparts. Therefore, you cannot interchange them. For example, normally you would call `for num in range(12)`. `async for num in range(12)` would not work. On the flip side, consider the documentation example from `aiofile`:
```Python
from aiofile import AIOFile


async def main():
    async with AIOFile("/tmp/hello.txt", 'w+') as afp:
        ...
```
You cannot call just `with AIOFile(...) as afp:`; you must use `**async** with AIOFile(...) as afp:`. More specifically, `with` will look for `__enter__` and `__exit__` under `AIOFile`, which do not exist so will result in error, while `async with` will look for `__aenter__` and `__aexit__`, which `AIOFile` does define for itself.

Note this strongly connects to Tip 2. You cannot generally call asynchronous library stuff in non-`async` blocks. Doing so will generally give you a bad time.
# Conclusion
Like any new programming concept, `async` Python will take time and practice to get used to.
