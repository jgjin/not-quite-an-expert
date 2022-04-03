---
title: "Trie-d chords"
date: 2022-04-02
draft: false
tags: ["programming", "language"]
---
In my [last post]({{< ref "chords.md" >}}), I explained the notion of "chords." English words analogize to "chords" of Latin letters. And as it turns out, thinking of English words as "chords" gives us a useful way to approach common problems involving English words.

Suppose you wanted to check if a "word" (sequence of Latin letters) exists in a dictionary. Naively, you could go through every entry in the dictionary. Clearly, this would take unpleasantly long for extremely large dictionaries.

If we think of words/entries in our dictionary as "chords" of Latin letters, we could represent each chord as a path in a graph of letters. For example:[^1]
[^1]: The numbers in blue represent example values we could associate with each complete chord.

![Example trie](https://upload.wikimedia.org/wikipedia/commons/b/be/Trie_example.svg)

After converting our dictionary into this form, to check if a "word" exists, we can try to follow the path. If we can follow the path to completion, the "word" exists. Otherwise, it doesn't exist in our dictionary (yet).

We call this data structure a ["trie" or "prefix tree."](https://en.wikipedia.org/wiki/Trie) As we alluded to in the beginning, it appears in efficient solutions to many common problems involving English words. Most notably, we use some version of a trie to efficiently implement autocorrect and string search ([Aho-Corasick](https://en.wikipedia.org/wiki/Aho%E2%80%93Corasick_algorithm)).

As I have framed, the trie generalizes beyond English words, to all populations representable as "chords." We can swap out the graph of letters with a graph of whatever constituents make up the "chords" or interest.
