---
title: "Monaspace"
date: 2025-07-12
draft: false
tags: ["technology", "language"]
---
From ["Font activations"](https://robhorning.substack.com/p/font-activations):
> [N]ow words are everywhere treated as data, as numbers, and they are rarely printed at all. ... Fonts perhaps become more salient as they become the only thing that people are expected to play with when it comes to words, once it is drilled into everyone that it is required for efficiency and political conformity to let machines do all the writing and the editing and the “thinking.”

So as I've started to rely more on large language models (LLMs) to write code, I've formed stronger opinions on fonts for code. From highest to lowest importance:
1. the font should distinguish between similar characters, e.g. between O and 0, between I and l and 1, for readability
2. the font should fix width of characters for readability - [monospaced](https://en.wikipedia.org/wiki/Monospaced_font)
3. the font should not have serifs, because [the conditions of text have changed](https://aresluna.org/the-hardest-working-font-in-manhattan) and [we no longer chisel letters into stone](https://en.wikipedia.org/wiki/Serif#Origins_and_etymology) - [sans serif](https://en.wikipedia.org/wiki/Sans-serif)

At first I used [Source Code Pro](https://github.com/adobe-fonts/source-code-pro), which satisfies the above. Then I used [Fira Code](https://github.com/tonsky/FiraCode), which satisfies the above and has programming ligatures. Now I use [Monaspace](https://github.com/githubnext/monaspace), which satisfies the above and has programming ligatures and ✨texture healing✨ (monospace-preserving [kerning](https://en.wikipedia.org/wiki/Kerning)), oooh!
