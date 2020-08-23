---
title: "Conditional probability and the gambler's fallacy"
date: 2020-07-11
draft: false
tags: ["misc"]
---
# Introduction
I've been playing a lot of Warframe. In the game, [loot is dropped according to predefined probabilities](https://warframe.fandom.com/wiki/Drop_Tables),[^1] so I've brushed up on my statistics.
[^1]: These drop probabilities are independent between trials (enemy defeated/mission completed).
# Conditional probability 
Formally, with events A and B, the probability of A knowing B has already occurred equals the probability A and B occur together divided by the probability B occurs with or without A. Let's see an example.
# Bird watching example
Last week, you went bird watching with your good friend Sam at Bird Park. You know there are exactly 100 birds in Bird Park, including 50 doves. 40 of those 50 doves are green, and the remaining 10 are yellow.

Let's define events A and B:

A: Sam saw a single green dove

B: Sam saw a single dove

Sam tells you she saw a single dove. However, the dove flew away too fast for her to discern the color. What's the probability she saw a single green dove, knowing she saw a single dove?

From the equation before, that probability is 40% / 50%, which comes out to 80%.

As a silly reversal, suppose Sam tells you she saw a single green dove. Knowing this, what's the probability she saw a single dove?
Obviously, since all green doves are doves, 100%. From the equation before, 40% / 40% comes out to 100%.
# Gambler's fallacy
Fairly confident she saw a green dove, Sam heads to Macau because green doves signify good luck. Sam decides to play roulette.

In roulette, there is almost 50% chance for red and almost 50% chance for black. To simplify, let's say the probabilities are exactly 50%, or 1/2.

Amazingly, the previous twelve rounds of Sam's game were all blacks! Should Sam put her money on red since the game is "due" for a red soon?

Let's define events A and B:

A: twelve blacks then one red

B: twelve blacks

From the previous equation, the probability that the next round will be red is (1 / (2^13)) over (1 / (2^12)), which comes out to 50%. That's the same probability of red as normal!
# Conclusion
The sad truth of conditional probabilities is that with independent trials, previous results do not influence upcoming results. In Warframe, if I didn't get a rare drop this time, then next time I'm not more likely to get it. In roulette, the game is never "due" for any particular result; the probabilities of each outcome stay the same. Sam decides to leave the table and keep her money for a better apartment. Meanwhile, with not much to do for the next few days, I'll play more Warframe, even if I'm never due for a rare drop.
