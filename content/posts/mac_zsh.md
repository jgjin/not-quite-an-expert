---
title: "Configuring my new Mac III: zsh / kitty"
date: 2021-03-27
draft: false
tags: ["technology"]
---
# Introduction
A command-line interface (CLI) lets you run lower-level operations ("commands") quickly and flexibly. For example, suppose I have 1000 .txt files that I wanted to rename only if they contain the word "banana" or "onion" (ignoring case, so "Onion" and "Banana" included). I can simply run 
```zsh
$ grep -l -i "banana|onion" | rename -s ".txt" "_with_food.txt"
```

If you do deep computer-y stuff often, you could probably benefit from learning to use CLI.[^1]
[^1]: Conversely, if you don't do much deep computer-y stuff, CLI might have a learning curve too steep to pay off.
# Choosing a shell / terminal
More formally, a terminal emulator (or simply "terminal"), a computer program, presents a command-line interface to run commands interactively in the computer's shell. However, most of the time you can use "terminal," "shell," and "command-line" interchangeably.

I use the [kitty terminal emulator](https://sw.kovidgoyal.net/kitty/) because it renders faster than the default macOS Terminal. kitty[^2] also offers tabs, which allow you to more easily run multiple commands simultaneously. This becomes especially useful when you have long-running commands, such as compiling a large code project.
[^2]: Computer-y stuff often gets stylized all lowercase.

I use Z shell (usually shortened to "zsh") because the community offers a plethora of nice plugins for zsh. As mentioned in [a previous post, which provides some more background on zsh]({{< ref "mac_homebrew.md" >}}), newer macOS versions come with zsh by default.
# zsh plugins
[Oh My Zsh](https://ohmyz.sh/) seems to have the greatest popularity of zsh configuration managers. However, I've found Oh My Zsh bloats my terminal startup time to multiple seconds. Instead, I use the more lightweight [Antibody](https://getantibody.github.io/).

According to [their instructions](https://getantibody.github.io/usage/#static-loading), you list your plugins in `~/.zsh_plugins.txt`, then run
```zsh
$ antibody bundle < ~/.zsh_plugins.txt > ~/.zsh_plugins.sh
```
and add 
```
source ~/.zsh_plugins.sh
```
to your `~/.zshrc` zsh configuration file.

My plugins list currently contains:
```
Aloxaf/fzf-tab
MichaelAquilina/zsh-auto-notify
MikeDacre/careful_rm
Tarrasch/zsh-bd
desyncr/auto-ls
hcgraf/zsh-sudo
hlissner/zsh-autopair
skywind3000/z.lua
zsh-users/zsh-autosuggestions
zsh-users/zsh-completions

# pure theme requires async
mafredri/zsh-async
sindresorhus/pure

# history substring search optionally requires syntax highlighting
zsh-users/zsh-syntax-highlighting
zsh-users/zsh-history-substring-search
```

Since the plugins .txt file comes in \<GitHub username\>/\<GitHub repository name\> format, you can just prefix `github.com/` to look up a plugin. For instance, [github.com/sindresorhus/pure](https://github.com/sindresorhus/pure) holds `sindresorhus/pure`
# Conclusion
I want to conclude with some tips for people new to terminal:
1. You probably won't use terminal for much in the beginning. However, anytime you find yourself doing something repetitive (e.g. renaming many files, as in the introduction), consider doing it through terminal. If it works out, write it down somewhere so you can use it again.
2. Don't worry too much about the "proper" way to use terminal. When my friend tried to get me to use Vim, they disabled the arrow keys to force me to use Vim's "more proper" hjkl. Today, I don't use Vim as my primary text editor. The best way to learn terminal starts with actually using terminal, in whatever way feels good to you, and refining from there.
3. Play around with the colors and fonts of terminal, maybe even the sounds. If it looks and sounds better (even if it functions the same), you'll use terminal more.[^3]
[^3]: This point actually comes from an online tutorial I pulled up when starting to learn guitar. The tutor mentioned many people fall out of practice because they get a guitar which doesn't sound good, and that discourages them.
