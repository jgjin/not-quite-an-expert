---
title: "Hand-over-hand locking with the RAII pattern"
date: 2020-04-18
draft: false
tags: ["programming", "technology"]
---
# Introduction
Let's start by explaining locks (mutexes), hand-over-hand locking, and RAII. Then we'll provide some sample code to accomplish hand-over-hand locking with the RAII pattern.
# Mutexes, a.k.a. locks
In concurrent programming, a common method for protecting shared resources between threads is using a mutex[^1], or lock. For everyday concurrent programming, there are 2 types of mutexes we care about:
[^1]: From smashing the words "mutual" and "exclusion" together
1. Regular mutexes

The regular mutex supports two main operations: `lock` and `unlock`. Once a thread `lock`s the mutex, any other call to `lock` the mutex is blocked until that thread calls `unlock`. This way, threads can prevent other threads from accessing or modifying a shared resource until the first-locking thread is done with the shared resource.

2. Shared mutexes, a.k.a. readers-writer locks

A shared mutex supports two more operations: `lock_shared` and `unlock_shared`. At any given point in execution, only one of the following can be true:
1. One thread has called `lock` on the shared mutex, and all other calls to `lock` and `lock_shared` will be blocked until that thread calls `unlock`. The thread is said to have/hold a "unique lock" on the shared mutex.
2. One or more threads have called `lock_shared` on the shared mutex. Any call to `lock_shared` will continue fine, while any call to `lock` will be blocked until **every** thread with `lock_shared` has called `unlock_shared`. The threads with `lock_shared` are said to have/hold a "shared lock" on the shared mutex.

The reason a shared mutex is also called a readers-writer lock is that it allows either one writer (through `lock` and `unlock`) **OR** any number of readers (through `lock_shared` and `unlock_shared`) to access a shared resource. Thus, another term for "unique lock" is "write lock"; another term for "shared lock" is "read lock."
# Hand-over-hand locking
One helpful way to view concurrent programming is that from the perspective of a particular thread, anything can happen in between when you unlock a mutex and when you next lock a mutex. Other threads can be, and usually are, doing things to the shared resource the current thread doesn't know about.

Some shared resources come in a hierarchical format. Perhaps the most well-known example is a file system. In the path `/not/quite/an/expert.txt`, the `expert.txt` file is below the `an` directory, which is below the `quite` directory, below the `not` directory, below the root directory (often denoted just `/`). When accessing a shared resource with a hierarchical format, using hand-over-hand locking prevents race conditions.

Here's how hand-over-hand locking works:
1. Obtain a lock on the root resource
2. Initialize the current resource to the root resource
3. For every component in the path from the root to target resource in the hierarchy,

    a. Verify the current resource has the next component in the path as a child
    
    b. Lock the next (child) resource
    
    c. Set the current resource to the next (child) resource

    d. Unlock the previous (parent) resource

4. Now with the lock on the target resource still active, do whatever core operation
5. Unlock the target resource

Don't worry, we have a code example at the end of this post. On a high level, by locking the next resource **before** you unlock the previous resource, you can ensure that something unexpected does **not** happen to the parent while you are trying to lock to the child. In our file system example, the hand-over-hand locking would prevent other threads from deleting the `an` directory before the current thread can lock the `expert.txt` file.
# RAII
RAII stands for resource acquisition is initialization. On a high level, in programming there often a "do" then "undo" pattern. In this case, there is a `lock` then `unlock` pattern. In complicated code, it is often hard to notice if every "do" has exactly one corresponding "undo." A common bug in concurrent programming is forgetting to `unlock` a mutex we `lock`ed. This mistake prevents any thread from accessing the shared resource in the future. Often this leads to a deadlock, in which your program is stuck forever. Whoops!

RAII takes advantage of the fact that in almost any modern programming language an object being created always calls the constructor of its class exactly once and the same object being deleted always calls the destructor of the class exactly once. If we call the "do" operation in the constructor and the "undo" operation in the destructor, we can ensure through the object that every "do" has exactly one corresponding "undo." In the case of mutexes, we can prevent deadlocks\![^2]
[^2]: However, it does not mean that using the RAII patten will automagically prevent deadlocks.

# And now, C++[^3]
[^3]: Side note: I don't love C++. Those who know me know I generally prefer Rust. It's just easier for me to demonstrate this particular idea with C++.

In C++, the standard library provides RAII mechanisms for mutexes. In line with the names we used before, the RAII mechanism that calls `lock` in the constructor and `unlock` in the destructor is `std::unique_lock`, and the RAII mechanism that calls `lock_shared` in the constructor and `unlock_shared` in the destructor is `std::shared_lock`. To create these RAII mechanisms, you pass the constructor the appropriate mutex:
```C++
#include <mutex>

...
std::mutex some_mutex;
std::unique_lock<std::mutex> some_unique_lock(some_mutex);
...
```
When `some_unique_lock` is constructed, it will call `some_mutex.lock()` and when it goes out of scope, it will call `some_mutex.unlock()`.

To combine hand-over-hand and RAII-based locking, we could use the following (somewhat abstracted) C++ code:
```C++
...
// Point A
std::unique_lock<std::mutex> current_lock(root_resource_mutex);

// Point B
auto &current_resource = root_resource;

// Point C
for (auto child_resource : path_from_root_to_target_in_hierarchy) {
    // Point D
    <check current_resource has child child_resource>

    // Point E
    std::unique_lock<std::mutex> child_lock(child_resource_mutex);
    
    // Point F
    current_resource = child_resource;

    // Point G
    current_lock.swap(child_lock);
}
// Point H
...
```
Let's break down the code. Recall the steps in hand-over-hand locking (from before, reproduced for convenience):
```
1. Obtain a lock on the root resource
2. Initialize the current resource to the root resource
3. For every component in the path from the root to target resource in the hierarchy,
    a. Verify the current resource has the next component in the path as a child
    b. Lock the next (child) resource
    c. Set the current resource to the next (child) resource
    d. Unlock the previous (parent) resource
4. Now with the lock on the target resource still active, do whatever core operation
5. Unlock the target resource
```
Step 4 would correspond with the ending ellipsis (Point H) of the previous C++ code block. The code block specifies code to accomplish steps 1-3 and 5. Point A relates to steps 1, B to step 2, C to step 3 in general, D to step 3a, E to step 3b, and F to step 3c.

Point G is the trickiest, as it relates to step 3d. The `swap` call swaps the mutexes held by the unique locks. By swapping the mutex held within `current_lock` with that held within `child_lock`, `current_lock` now holds the next (child) mutex while `child_lock` now holds the previous (parent) mutex. When the for loop iteration ends right after Point G, `child_lock` goes out of scope, thereby calling `unlock` on the previous (parent) mutex. Since `current_lock` is still in scope, we correctly avoid calling `unlock` on the next (child) mutex until next for loop iteration finishes. Since `current_lock` stays in scope for the duration of the entire code block, it allows us to perform step 4 before `current_lock` goes out of scope, at which point it would call `unlock` on the target resource (step 5).
# Conclusion
This is just one example of how concurrent programming can get complicated. While a raw `lock` and `unlock` alternate implementation of the code example could work, it would be prone to not preserving exactly one `unlock` per `lock`, leading to potential race conditions or deadlocks. Concurrent programming is significantly harder than serial programming because it introduces much more non-deterministic code, and creates opportunities for whole new classes of bugs, like race conditions and deadlocks. Be extremely vigilant when writing concurrent code!
