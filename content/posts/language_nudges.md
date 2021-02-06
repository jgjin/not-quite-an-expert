---
title: "(Technical) language nudges thought"
date: 2021-01-23
draft: false
tags: ["language", "philosophy", "programming", "technology"]
---
[Continued in this post]({{< ref "medium_nudges.md" >}})
# Introduction
The Sapir-Whorf hypothesis states that the structure of a language influences the cognition of its speaker. Now, before we proceed, I caution that ["there is probably no single linguistic idea that is more prone to exaggeration and mis-application than the 'Sapir-Whorf hypothesis.'"](https://languagelog.ldc.upenn.edu/nll/?p=2592) Let's see an example.
# Whodunit?
In English, you would tend to say "They broke the vase," whereas in Spanish you would more tend to say "El jarrón se rompió" ([example](https://elperiodico.com.gt/opinion/opiniones-de-hoy/2020/05/14/el-jarron-se-rompio/)), which literally translates to "the vase broke itself" ([paywall](https://www.wsj.com/articles/SB10001424052748703467304575383131592767868)).

You might claim, as the WSJ article did, that Spanish speakers have "a different sense of blame." However, less sensationally and more accurately, we find that Spanish speakers attribute blame less often. This holds ["even when people have rich established knowledge and visual information about events."](https://www.researchgate.net/publication/47635862_Subtle_linguistic_cues_influence_perceived_blame_and_financial_liability) 
# Technical languages 
Natural languages have rich, complex structure meant to express ambiguity and emotion. In contrast, technical languages, particularly programming languages, by design focus on relatively limited (with few absolute rules and keywords), unambiguous syntax. 

For example, in C++, you might code:
```C++
struct Person { ... };
...
void blame(Person& target) { ... }
...
Person james(/*name=*/"James");
blame(james);
```
Unlike in English or in Spanish, in C++ we must know "who deserves blame" (i.e. the value of argument `target` in `blame`). The programming language specification very strictly sets out how we can express ideas, in order to translate (compile) into machine instructions.
# Conclusion
I hypothesize that the Sapir-Whorf hypothesis applies even more strongly to technical language. In a technical language, you have a "way to do things," and that way to do things strongly influences how you think about and solve problems in the technical domain.
