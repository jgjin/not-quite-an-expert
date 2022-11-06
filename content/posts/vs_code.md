---
title: "From Emacs to VS Code"
date: 2022-11-05
draft: false
tags: ["programming", "technology"]
---
Recently, I switched from Emacs to VS Code as my primary text editor. For those who knew me well, this represents a significant change.
# I: Eclipse eclipsed
For my first in-person[^1] programming course, I used Eclipse. Slow and mangled by a maze of menus, Eclipse made my laptop scream and boil. I asked [my friend U]({{< ref "taquito.md" >}}) for another editor to use.
[^1]: I had casually taken some online courses in middle school.
# II: Vim, then Emacs, then Emacs-Vim
U suggested I use Vim. To make sure I _properly_ used Vim, U disabled the arrow keys in favor or hjkl, the Vim convention for moving around. Instead of learning to use hjkl, I learned to dislike Vim, and asked U for yet another option.

"Well, you could always use _Emacs_," U huffed, like a Montague to a Capulet. For reasons lost to ancient computer history (1980s!), Vim hates Emacs, and vice versa - the "Holy Text Editor War." In the eyes of Vim, Emacs stood for "Escape Meta Alt Control Shift" (Emacs uses many modifier keys) or "Eight Megabytes And Constantly Swapping" (a lot of memory for the time, which should date the War as ancient) or "EMACS Makes Any Computer Slow" (a recursive acronym like GNU - GNU's Not Unix!, both created by Richard Stallman). Versus an editor in which I could not efficiently move, Emacs felt modern and easy.

So I have used Emacs since high school. I first wrote my own Emacs configuration. Once that reached over 1000 lines, I did not want to maintain it, so I switched to [Spacemacs, a community-developed configuration](https://spacemacs.org/).

Spacemacs emulates Vim navigation, so I guess I did learn hjkl in the end.[^2] Emacs Montague and Vim Capulet united as one, Spacemacs assimilated the efficiency of Vim into the richness of Emacs. Over the years, I learned to love the aesthetic, and principles of Emacs. ["Free as in Freedom"](https://en.wikipedia.org/wiki/Free_as_in_Freedom), as Richard Stallman, Emacs father and [famous foot-eater](https://www.youtube.com/watch?v=I25UeVXrEHQ), would declare.
[^2]: Now I actually emulate Vim navigation in my web browser (via [Vimium](https://vimium.github.io/)) too!
# III: VS Code, free as in Micro$oft
At Google, I could use Emacs to write code and Code Search to read code. However, at Glean, we don't (yet?) have swaths of people to maintain a code search tool.

As migrants from another time, Emacs and Vim don't support code navigation for large code bases very well out of the box. Thankfully, the [Language Server Protocol](https://microsoft.github.io/language-server-protocol/) defines editor-agnostic language analysis, with enthusiastic support across many languages. However, Emacs and Vim don't quite support LSP as well as other editors _yet_.

So as I was navigating our code base with my manager, I had to (with some embarrassment) manually search each function and class name. I feel comfortable personally dealing with the quirks of Emacs; I don't feel comfortable imposing Emacs inconveniences on others.

It took me a bit of time to emotionally distance myself from Emacs, my partner in code throughout high school and college. After researching that VS Code can emulate the behaviors I want from Spacemacs (see [VSpaceCode](https://vspacecode.github.io/)), I checked VS Code would support code navigation like I wanted, and it did.

With some tweaking, sans some minor annoyances (like handling buffers and frames differently than Emacs), I've turned my VS Code into Spacemacs + code navigation, free as in Micro$oft.