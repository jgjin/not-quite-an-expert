---
title: "async Python"
date: 2020-08-15
draft: false
tags: ["technology"]
---
# Introduction
Often, hardware I/O bottlenecks a program's latency, thoroughput, and/or run time. That is, file and network actions slow down the program most significantly. A program often consists of many independent file and network actions. In those cases, Python gives us the `async` construct for running the actions asynchronously.
# Syntax
In general, `async` Python programming involves adding the keyword `async` in front of the non-asynchronous version. Instead of `def`, use `async def`. Instead of `with`, use `async with`. Finally, instead of `for`, use `async for`.

Let's look at some examples for the bottlenecks I mentioned before.
# `async` file I/O
In general, to read files:
```Python
from aiofile import AIOFile, LineReader


async def async_read_whole(file_name):
    async with AIOFile(file_name, "r") as afp:
        print(await afp.read())


async def async_read_lines(file_name):
    async with AIOFile(file_name, "r") as afp:
        async for line in LineReader(afp):
            print(line)
```
I'll cover `await` in my next post in more detail. For now, know that we should `await` every `async` function call.

To write files:
```Python
from aiofile import AIOFile, Writer


async def async_write(file_name):
    async with AIOFile(file_name, "w") as afp:
        writer = Writer(afp)

        await writer("Hello")
        await writer(" ")
        await writer("World")
        await afp.fsync()
```
The `fsync` call syncs changes to the file so other accessors can see those changes.
# `async` network I/O
To make network requests:
```Python
import asyncio

import aiohttp


async def async_get(session, url):
    async with session.get(url) as response:
        return await response.text()


async def async_main():
    urls = [
        "https://en.wikipedia.org/wiki/Async/await",
        "https://en.wikipedia.org/wiki/Coroutine",
        "https://en.wikipedia.org/wiki/Event_loop",
    ]
    async with aiohttp.ClientSession() as session:
        response_coros = list(map(lambda url: async_get(session, url), urls))
        for response_coro in asyncio.as_completed(response_coros):
            response_text = await response_coro
            print(response_text[:144])
```
A couple things to note:
1. We reuse `session` to avoid redundantly establishing network connections.
2. The `asyncio.as_completed` pattern avoids `await`-ing each `async` request to complete before the next `async` request, which would negate the benefit of `async`.
3. We can replace `get` in `session.get` with any HTTP request method, e.g. `post`.
# Conclusion
Hopefully, this provides you some good introductory motivation and examples for `async` programming in Python.
