---
title: "Configuring my new (new) Mac I: Homebrew"
date: 2021-01-16
draft: false
tags: ["technology"]
---
# Introduction
In this series of posts I'd like to walk through the process of configuring my new Mac to
1. help out anyone else configuring their Mac
2. interrogate and reflect on the decisions I make during configuration
3. provide a stable reference to conveniently come back to if needed
# Package managers
Like mobile app stores, package managers provide a single place to manage installing, updating, and removing programs. Package managers, however, tend to have more of a open-source, power user connotation. Coming from Arch Linux, I like and prefer to use a package manager.

Before I tell you about installing my package manager, however, let's talk about arm and x86.
# arm and x86
At the lowest level, computers run "instructions," e.g. load, store, and add. To run a program on a computer, you must compile or interpret the program as lines of these instructions. An instruction set architecture (ISA) specifies the types of instructions a computer can run and how those instructions should behave.[^1]
[^1]: An ISA also specifies data types, registers, etc. outside the scope of this post.

You will generally encounter 2 ISAs with everyday computers: smartphones mostly run arm (yes, stylized all lowercase); laptops and desktops mostly run x86. Until the most recent generation, Macs have run x86. However, the newest Macs run arm, with support for x86 programs via Rosetta 2, an emulator that translates from x86 to arm.

Because historically laptops and desktops have mostly run x86, many developer-oriented programs, libraries, even programming languages, lack support for arm. With that in mind, I want the following from my package manager:
1. Availability of packages I want/need
2. Ease of package installation, update, and removal
3. Support for arm where available with x86 as backup
4. Commitment to support arm going forward
# Step 1: fail
In my first attempt, I dipped my toes into the Nix package manager. However, using Nix to install yabai, a tiling window manager, ended up so messy that I felt the need to purge my Mac. To clean the slate, [I tried to re-install macOS, bricked the laptop, and had to return it]({{< ref "jank.md" >}}). I write to you from a second Mac.
# Step 2: choose a different package manager
Since I got burned by Nix,[^2] I'll use the go-to package manager for Mac: Homebrew, which fulfills the 4 requirements from before. The next steps come from [this tutorial by Sam Soffes](https://soffes.blog/homebrew-on-apple-silicon), with some additional steps and explanation where I feel appropriate.
[^2]: Yes, you could argue I misunderstood or misused Nix. Regardless, I have a bad association. Nix also requires a lot of manual configuration, much more than other package managers.
# Step 3: install Rosetta 2
For x86 compatibility, we install Rosetta 2. In terminal:[^3]
```sh
$ softwareupdate --install-rosetta
```
[^3]: You might see `$` notation when people talk about executing commands in terminal. The dollar sign stands for the shell prompt, where you type commands to execute. In the default terminal, your prompt might have `%` instead of `$`.
# Step 4: install x86 Homebrew
Next in terminal:
```sh
$ arch -x86_64 /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install.sh)"
```
Only run commands you understand and trust. This command means:
1. arch -x86_64: With Rosetta 2 to emulate x86,
2. /bin/bash -c: use the bash shell to run 
3. "$(curl ...)": the install script from Homebrew

This will set up Homebrew for x86 packages in `/usr/local`. 
# Step 5: install arm Homebrew
Homebrew expects to install arm packages in `/opt/homebrew`, so let's create that directory:
```sh
$ sudo mkdir -p /opt/homebrew
```
Since `/opt/homebrew` lives on the system level rather than the user level, we request special permission to create the directory by prefixing the command with `sudo`, meaning "superuser do."

We set ourselves as the owner of that directory:[^4]
```sh
$ sudo chown -R $(whoami):staff /opt/homebrew
```
[^4]: `staff` refers to the default group of all users in Mac, so technically we change the owner to the default group of all users.

Finally, we go into `/opt` and install our arm Homebrew in `/opt/homebrew`:
```
$ cd /opt && curl -L https://github.com/Homebrew/brew/tarball/master | tar xz --strip 1 -C homebrew
```
# Step 6: Set `PATH`
(Almost) all commands you run in terminal actually come from files in your computer. The `PATH` environment variable specifies where to look for files for commands. 

Recall we installed our arm Homebrew in `/opt/homebrew` and our x86 Homebrew in `/usr/local`. Also recall I would prefer arm first and x86 as backup. Therefore, we put our arm Homebrew directory before our x86 Homebrew directory in our `PATH`.

Where should we modify the value of `PATH`? Newer macOS versions have moved from bash to zsh for terminal. [This post describes the configuration files for zsh in detail](https://scriptingosx.com/2019/06/moving-to-zsh-part-2-configuration-files/). Short answer: let's set `PATH` in the file `~/.zshrc`:[^5]
```sh
$ touch ~/.zshrc && echo 'export PATH="/opt/homebrew/bin:/usr/local/bin:$PATH"' >> ~/.zshrc
```
[^5]: `~` stands for the user's home directory.

`touch` creates the file and `echo` puts the export statement into that file.

We need to run our x86 Homebrew through Rosetta 2. For convenience, we can create an alias `ibrew` to properly run our x86 Homebrew. Add 
```sh
alias ibrew='arch -x86_64 /usr/local/bin/brew'
```
to a relevant zsh configuration file. For example, to add it to our `~/.zshrc` we can use `echo` again:
```sh
$ echo "alias ibrew='arch -x86_64 /usr/local/bin/brew'" >> ~/.zshrc
```
# Conclusion
To install an arm package, we can run `brew install <package>`. In case Homebrew can't find an arm version, we can run `ibrew install <package>` to install the x86 version.

Going forward, Homebrew will provide greater support for arm, since Apple has decided to use arm for future Macs. These instructions may soon become out-of-date.
