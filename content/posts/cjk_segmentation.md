---
title: "CJK segmentation"
date: 2021-10-02
draft: false
tags: ["language", "technology", "groups", "people"]
---
# Introduction
Let's return to [the Unicode Standard](https://www.unicode.org/versions/Unicode14.0.0/ch18.pdf) from the last post:
> Programmers do not expect the characters c, h, a, and t alone to tell us whether chat is a French word for cat or an English word meaning informal talk. ... Similarly, the Han characters are often combined to "spell" words whose meaning may not be evident from the constituent characters. For example, the two characters "to cut" and "hand" mean "postage stamp"" in Japanese, but the compound may appear to be nonsense to a speaker of Chinese or Korean.
![Han spelling](/han-spelling.jpg)

Like with Latin letters, Han characters take meaning from context, and change meaning between different contexts.
# Segmentation
I want you to go to [the homepage of _The New York Times_ in Chinese](https://cn.nytimes.com/). You might notice something missing: spaces. Well, not missing entirely. However, certainly fewer spaces than English text, and not spaces between every word.

Unlike English text, Chinese text does not by convention separate words within a phrase with spaces. This can create ambiguity, such as [this example](https://www.hathitrust.org/blogs/large-scale-search/multilingual-issues-part-1-word-segmentation):
![Ambiguous Chinese sentence](https://www.hathitrust.org/sites/www.hathitrust.org/files//CJK_teehan_brocolli.png)

The first segmentation means "I like New Zealand Flowers." The second segmentation means "I like fresh broccoli." However, in almost all cases a human reader can extract the words from context. 

This presents a significant challenge when parsing and understanding Chinese text with computers. The problem of separating Chinese characters into words, called segmentation, represents an open problem in natural language processing. You can perform pretty well in natural language processing English text by naively splitting on spaces and punctuation. You will get terrible results doing the same in Chinese. Accurate segmentation requires large dictionaries, or more recently, machine learning models trained on large corpora of (generally pre-human-segmented) Chinese text.
# Conclusion
I encourage you to look into CJK segmentation, to reason about what performs best and why.
