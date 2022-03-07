---
title: "Stenotype"
date: 2022-02-26
draft: false
tags: ["technology", "programming"]
---
When building a keyboard, you make a lot of choices. 

Organizationally, how many keys,[^1] and do we align[^2] or stagger them? 
[^1]: "Full size" keyboards have around 100 keys, so 40% have around 40 and 60% keyboards have around 60 keys.
[^2]: We call keys aligned in straight columns "ortholinear."

Structurally, does the keyboard split (one side per hand), and do we need a wire?

Compositionally, what can we take apart (and replace), and what materials do the parts contain?

However, perhaps the most meaningful choice we can make centers around function. _What happens when we press keys?_

You know [the QWERTY layout]({{< ref "qwerty.md" >}}); you may even know [the Dvorak layout]({{< ref "dvorak.md" >}}). However, your keyboard does more than just send characters. It also handles "modifiers": shift, control, alt/option, super/windows/command. Your keyboard can interpret and send combinations of keys, e.g. shift+f = F or control+C.

I want to do something _wild_ (and vaguely useful) with my keyboard. When we want _wild_, we can ask: _What do wild users do?_ Wild users, as in those with unusual (unforeseen) and/or performance-heavy use cases, can stretch our imagination in how we build and use something.

In the case of keyboards, we look at stenographers, who type as fast as people speak in real time. They document important events as they happen, such as court cases and medical conversations. A trained stenographer must reach around 200 words per minute (wpm), and some reach over 300 wpm!

Professional stenographers don't usually use QWERTY, nor Dvorak. They use a special machine, a stenotype keyboard. The stenotype keyboard has fewer keys than a conventional keyboard, which you press simultaneously (known as "chording") as a shorthand for words and phrases.
![stenotype layout](https://upload.wikimedia.org/wikipedia/commons/8/8c/Stenotype_en_layout.svg)
These shorthand "chords" often follow phonetics, not spelling. For example, S+E+Z can produce "says" and "was" can come from W+A+S or W+U+Z. The translation of chords to words/phrases depends on how you configure the stenotype machine.

I have a [QMK](https://github.com/qmk/qmk_firmware/blob/master/docs/feature_stenography.md) keyboard, capable of stenography using [the open source Plover stenography engine](https://www.openstenoproject.org/plover/). I will try stenography, and we will see how it goes.
