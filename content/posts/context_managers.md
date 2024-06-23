---
title: "Context managers in Python"
date: 2020-08-08
draft: false
tags: ["technology"]
---
# Introduction
In programming, we often need to manage resources that "open" and "close." As a simple example, you might open a file, read its contents, then close it.

However, when dealing with complicated paths of logic, we may find it difficult to reason about manually closing every open resource. Consider:
```Python
import json


def get_json_data(json_file_name):
    # we must eventually close this opened file
    json_file = open(json_file_name, "r+")

    try:
        json_data = json.load(json_file)
    except json.JSONDecodeError as decode_err:
        # close here
        json_file.close()
        raise ValueError(
            f"{json_file_name} does not contain valid JSON: {str(decode_err)}")

    if "error" in json_data:
        # close here
        json_file.close()
        raise KeyError(
            f"JSON data contains \"error\" key with value \"{json_data['error']}\""
        )

    if "status" in json_data:
        status = json_data["status"]
        if status != "new":
            # close here
            json_file.close()
            raise ValueError(
                "JSON data contains \"status\" key with value "
                f"\"{json_data['status']}\", while value \"new\" expected")

        try:
            validate(json_data)

            json_data["status"] = "old"
            # overwrite json_file contents with status "old"
            json_file.seek(0)
            json_file.truncate()
            json.dump(json_data, json_file)

            # close here
            json_file.close()
            return json_data

        except KeyError as validation_error:
            # close here
            json_file.close()
            raise KeyError(
                f"JSON data validation failed on key {str(validation_error)}")

    # close here
    json_file.close()
    raise KeyError("JSON data does not contain \"status\" key")
```
Viewing this example code from just a high level, we can notice that properly dealing with the complicated paths of logic involves many calls to close the opened file.

In practice, we may have an even more complicated set of paths to deal with, and we could easily either double-close a file or forget to close it at all.
# Why do we need to close, anyway?
Besides clean-looking code, we should close open resources for a couple of reasons:
1. Changes in resources that need opening and closing often do not go into effect until we close the opener. Notably files have this behavior.
2. Resources that need opening and closing often "lock up" while opened, preventing other threads, processes, and machines from accessing them until the opener closes.
3. Opened resources consume hardware resources, particularly RAM, so can slow down the program.
# `with` statements
`with` statements in Python automatically close the opened resource when the block ends.[^1] Let's see the code now with `with`:
[^1]: whether through falling off the bottom or jumping (i.e. `break`/`continue`/`return`)
```Python
def get_json_data(json_file_name):
    with open(json_file_name, "r+") as json_file:
        try:
            json_data = json.load(json_file)
        except json.JSONDecodeError as decode_err:
            raise ValueError(
                f"{json_file_name} does not contain valid JSON: {str(decode_err)}"
            )

        if "error" in json_data:
            raise KeyError(
                f"JSON data contains \"error\" key with value \"{json_data['error']}\""
            )

        if "status" in json_data:
            status = json_data["status"]
            if status != "new":
                raise ValueError(
                    "JSON data contains \"status\" key with value "
                    f"\"{json_data['status']}\", while value \"new\" expected")

            try:
                validate(json_data)

                json_data["status"] = "old"
                # overwrite json_file contents with status "old"
                json_file.seek(0)
                json_file.truncate()
                json.dump(json_data, json_file)

                return json_data

            except KeyError as validation_error:
                raise KeyError(
                    f"JSON data validation failed on key {str(validation_error)}"
                )

        raise KeyError("JSON data does not contain \"status\" key")

```
On a high level, we can basically forget about closing, as the `with` statement construct does that for us auto-magic-ally. Actually, under the hood, `with` statements rely on the context manager construct in Python, which define `__enter__` to open a resource and `__exit__` to close that resource.
# Conclusion
You should use `with` statements where possible in Python. If you are using them already, hopefully now you know more about why.
