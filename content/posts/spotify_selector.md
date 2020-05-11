---
title: "Let's build a random Spotify album selector!"
date: 2020-05-09
draft: false
tags: ["programming", "technology", "art", "music"]
---
# Introduction
You read the title; let's do it!
# Meta-goals
I have 2 main goals for this post:
1. Describe the process of making something.
2. Write some code! I've been itching to code for a while.
# So how do (should[^1]) we make something?
[^1]: according to not quite an expert, of course
## Audience
Let's start with the most basic, perhaps most important question:
> Who's this for, anyway?

How you answer this question strongly influences every step of making something. Different audiences have wildly different needs, behaviors, and environments. For example, making food for newborn babies is vastly different than making food for 30-something adults. A newborn baby needs food that can be digested without chewing; the 30-something makes their own decisions on where and when to consume food.

Often times, we are asked to make something for _Not Me_. _Not Me_ is harder to understand than _Me_. However, if you build something for _Me_, _Not Me_ isn't going to be happy with your hand-me-downs.

For our Spotify album selector, the audience is Spotify users.
## Audience need
Now we move onto the next question:
> What do they need?

Once again, how you answer this question influences the rest of making something. Someone who needs food solely for nutrition might consider Soylent. Another who needs food to help them relax after work would probably gravitate more toward restaurant take-out.

In the interest of honest process, the description of need should avoid implying a solution. [In the famous words of HBS professor Theodore Levitt:](https://hbswk.hbs.edu/item/what-customers-want-from-your-products)
> People don’t want to buy a quarter-inch drill. They want a quarter-inch hole!

If these people could get a quarter-inch hole through cheaper, quicker, more convenient means[^2], the quarter-inch market would be in trouble!
[^2]: I often like to use "breaking watermelons over their heads" to signify abstract means: If people could get a quarter-inch hole by breaking watermelons over their heads, people would buy more watermelons and fewer drills!

In our case, Spotify users need to listen to albums across their large saved library. Note this need does limit our audience; it implies our audience saves a lot of whole albums to their Spotify library and listens to albums (rather than sporadic tracks) as units, which is not true of all Spotify users.
## Existing solutions w.r.t. audience need
Since making something takes time (and often money), it's worthwhile to check how your target audience currently does or can solve their need. It's unlikely you've encountered an entirely new need; almost certainly the existing solution(s) are not adequate on some dimensions, such as discoverability, access, and performance.

It's important to define how your solution will compare to existing solutions. Particularly,
> Why would the audience with the need want to use your solution instead of any other?

In this case, there does already exist a sufficient solution: [someone else has already built a random Spotify album selector](https://www.nativenoise.co.za/spotify/album-selector/). However, this does not solve _my_ need of wanting to code, so I'll proceed.
## Building
I'll get to the detailed building (programming) later. Here, I think it's worth mentioning the Agile process. If you work in programming, you'll likely end up working under some Agile variant one way or another. It's worth refreshing yourself once in a while.
## Iterating, maintaining
For non-one-off products, you'll likely need to build something multiple times before you get it right. To quote another famous source, [_The Mythical Man-Month_ by Fred Brooks:](https://course.ccs.neu.edu/cs5500f14/Notes/Prototyping1/planToThrowOneAway.html)
> In most projects, the first system built is barely usable. ... Hence plan to throw one away; you will, anyhow. 

Also, if many people over time are using the something, you'll definitely need to spend time and effort (and probably money) maintaining the something. Keep that in mind when building.
## Deprecating
Eventually, the something you made must go away. It's not always sad; perhaps it's going away in favor of the next version! Either way, if you're responsible for making something that a lot of other people use, please be kind and give them a grace period (a deprecation period in software) to find alternatives.
# Conclusion
Ha! I tricked you! Well, not really. I know you were expecting code in this post. I was too, actually. While writing I came to realize that the approach before you write your first line of code is incredibly important. In the next post I promise we'll write some code.
