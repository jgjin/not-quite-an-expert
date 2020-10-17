---
title: "Context managers in Python classes"
date: 2020-10-03
draft: false
tags: ["programming", "technology"]
---
# Introduction
[Context managers in Python allow us to easily manage opening and closing resources]({{< ref "context_managers.md" >}}). However, sometimes we want to define classes that use these context managers, in which case the `with` statement construct will not work.
# Synchronous example
Consider a class that manages data from multiple files:
```Python
class DataManager:
    def __init__(self, input_file_names, output_file_name):
        self.inputs = list(
            map(lambda input_file_name: open(input_file_name, "r"),
                input_file_names))
        self.output = open(output_file_name, "w")
    ...
```
In this form, we open and fail to close the files. See ["Why do we need to close, anyway?" from this post]({{< ref "context_managers.md" >}}) for reasons we should always close opened resources like files.

By instinct, you may consider using the destructor to close the files. However, Python does not provide any guarantee of when objects get garbage collected (i.e. when the destructor gets called). Python provides a better way with `contextlib.ExitStack`:
```Python
class DataManager(contextlib.ExitStack):
    def __init__(self, input_file_names, output_file_name):
        super().__init__()
        self.input_file_names = input_file_names
        self.output_file_name = output_file_name

    def __enter__(self):
        super().__enter__()
        self.inputs = list(
            map(
                lambda input_file_name: self.enter_context(
                    open(input_file_name, "r")), self.input_file_names))
        self.output = self.enter_context(open(self.output_file_name, "w"))

        return self
```
This form allows us to use our own class as a context manager:
```Python
with DataManager(["input1.txt", "input2.txt"], "output.txt") as data_manager:
    ...
```
By calling `.enter_context` on each of the context managers owned by our own class, we ensure the corresponding resources get closed when the `with` statement of our own class ends.
# Asynchronous example
Let's say you live on the cutting edge of Python, and use `async`. Python `contextlib` provides a class for that as well:
```Python
import contextlib
from aiofile import AIOFile


class AsyncDataManager(contextlib.AsyncExitStack):
    def __init__(self, input_file_names, output_file_name):
        super().__init__()
        self.input_file_names = input_file_names
        self.output_file_name = output_file_name

    async def __aenter__(self):
        await super().__aenter__()
        self.inputs = [
            await self.enter_async_context(AIOFile(input_file_name, "r"))
            for input_file_name in self.input_file_names
        ]
        self.output = await self.enter_async_context(
            AIOFile(self.output_file_name, "w"))

        return self
```
Recall the tips from [this post]({{< ref "async_tips.md" >}}):
1. `await` every `async`, except one.
2. `async` can include non-`async`. Non-`async` cannot include `async`.
3. You cannot interchange `async` and non-`async` constructs.

From Tip 1, make sure to call `await` on `.enter_async_context`. Note you cannot call `await` within `lambda`s, so I have replaced the `list(map(lambda ...))` with a list comprehension (i.e. `[expr(elt) for elt in iterable]`).

From Tip 2, you can call `.enter_context` within classes that inherit from `contextlib.AsyncExitStack` to manage synchronous context managers. However, you cannot call `.enter_async_context` within classes that inherit from `contextlib.ExitStack`.

From Tip 3, make sure to call your class that inherits from `contextlib.AsyncExitStack` with `async with`, NOT just `with`:
```Python
import asyncio


async def main_async():
    async with AsyncDataManager(["input1.txt", "input2.txt"],
                                "output.txt") as async_data_manager:
        ...


if __name__ == "__main__":
    asyncio.run(main_async())
```
# Conclusion
In general, whether you prefer more functions or more classes, Python context managers provide a safe, easy way to open and close resources.
