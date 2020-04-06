---
title: "Voting, and system design matters"
date: 2020-03-28
draft: false
tags: ["people", "groups", "management"]
---
# Introduction
In the United States, we use plurality (winner-takes-all) voting. However, this is just one of multiple single-winner voting systems that exist. Other voting systems include:
1. Instant-runoff voting
2. Approval voting
3. Borda count voting
4. Positive-negative voting
# A review of plurality voting
In a plurality voting system, the candidate with the most votes wins. Plurality voting is generally the most simple system. However, this simplicity is not always a virtue. The minimum number of votes a candidate needs to win under plurality voting is:

>[floor](https://mathworld.wolfram.com/FloorFunction.html)(number of votes / number of candidates) + 1[^1]

Therefore, it is possible for a candidate of whom a majority of voters[^2] disapprove to win. Let's say candidates A, B, C, D, and E get 21%, 20%, 20%, 20%, and 19% of the vote, respectively, and the 79% of voters who did not vote for candidate A absolutely disapprove of candidate A. Candidate A would still win, despite being disapproved of by 79% of voters. Obviously, this is a pathologically constructed example. However, it demonstrates outcomes like this are _possible_ under plurality voting.
[^1]: Assuming number of votes is greater than the number of candidates.
[^2]: For simplicity, let's assume each voter has exactly one vote.
# Instant-runoff voting
In instant-runoff voting, voters rank the candidates. Until a majority is reached:
1. Allocate each voter's vote to the highest-ranked candidate still remaining.
2. If a majority is not reached, remove the candidate with the least votes.

Instant-runoff voting prevents cases in which a candidate of whom a large majority strongly disapprove wins by plurality and not majority. In our previous example, candidate E would be removed, and candidate A would likely be removed in the next round.
# Approval voting
In approval voting, each voter can vote 0 to (number of candidates) times, giving either 0 votes or 1 vote to each candidate. Interestingly, since voters can vote multiple times, it is possible for multiple candidates to receive votes from a majority of voters. The winner under approval voting is the candidate who received the most votes.
# Borda count voting
In Borda count voting, voters rank the candidates (like in instant-runoff voting). For each voter's ballot, each candidate is given points equal to the number of candidates they rank above. For example, if I ranked from most to least preferred:

> A then C then D then B,

A would get 3 (ranked above C, D and B) points, C would get (ranked above D and B) 2 points, D would get 1 (ranked above B) point, and B would get 0 (ranked above no other candidate) points from me. The winner is the candidate with the most amount of points.
# Positive-negative voting
Positive negative voting allows each voter to assign +1 point to one candidate and -1 point to one candidate. Like Borda count voting, the winner is the candidate with the most points. Positive-negative voting can prevent some (though not all) cases in which a candidate of whom a large majority strongly disapprove wins by plurality and not majority. While it does not prevent all cases, it maintains relative simplicity.
# Ties and other complications
None of the systems above definitively describe the procedure to handle ties. In general, we assume there are so many voters that a tie is unlikely, or we may employ a backup tie-breaking vote. In some systems, we may also employ a runoff, in which the candidate with the least number of points or votes is removed and votes are recast as necessary, given there exists a candidate who is not tied for first.

In my descriptions of the voting systems, I had implicitly assumed each voter had exactly one vote. However, some systems give voters multiple votes (e.g. shareholder voting in the US, which is often weighted by number of shares owned by the voter). Allowing voters to have multiple votes can lead to _weirdness_,[^3] where the dynamics between voters and their interests become even more complicated.
[^3]: What sort of weirdness? Just general, hand-wavy weirdness. I'm not quite an expert!
# Why it matters
Depending on what type of voting a system uses, the system can bias radical or moderate candidates. As a whole, instant-runoff, approval, Borda count, and positive-negative voting tend to favor more moderate candidates than pure plurality voting. Therefore, for people in charge of determining how voting will work in some system, the choice of voting scheme implies a preference for more radical or more moderate candidates. In addition, the use of ranked (instant-runoff and Borda count) and positive-negative voting allow people to express strength of preference, which may or may not be useful. There cannot be a wholly unbiased voting system; the choice of voting system biases a certain type of candidate.
# A practical example: the US Presidential Election
Let's look at arguably _the_ most important[^4] plurality election: the US Presidential Election.
[^4]: For me, at least. I know this smacks of American exceptionalism, so I added the word "arguably."
## Encouraging radicalism
One claim we can make about the presidential primary system, in which parties choose their one candidate to run for President, is that it encourages radicalism more than the alternatives I described before. As we demonstrated before, the plurality system encourages candidates to race for a committed plurality, potentially even at the cost of lack of approval or disapproval from the remaining majority. Combined with the fact that primary voters are more ideologically extreme than the average American voter overall, we can see the primary system design favors more radical candidates.

Note that I'm not claiming this is the only or even a major reason why [recent data suggests Americans are becoming more polarized](https://www.pewresearch.org/topics/political-polarization/). Nor am I making a direct value judgement on extreme ideology.[^5]
[^5]: Even though it seems to me most people would abstractly prefer moderation, I think the point is more subtle, and deserves its own post.
## Strategic voting
Another feature of the US Presidential Election system is that it strongly encourages strategic voting for third-party aligned voters. First, some definitions:
1. **Sincere voting**: voting according to one's actual preferences. In a plurality system, this amounts to voting for your most preferred candidate, disregarding how others will vote.
2. **Strategic voting**: voting in order to optimize an election's outcome with respect to one's preferences, taking into account perceptions on how others will vote.

Note that no voting system is theoretically perfect. [The Gibbard–Satterthwaite Theorem](https://en.wikipedia.org/wiki/Gibbard%E2%80%93Satterthwaite_theorem) states that assuming no single voter can independently choose a winner and there are more than two options, a voting system is always susceptible to strategic voting.

For nearly the entirety of its existence, the Presidential Election has been divided into two parties. Since there is only one winner and no runoffs, a voter who prefers a third-party candidate the most is strongly incentivized to cast a strategic vote over their sincere vote. To make their vote count, they are pushed toward voting for their more preferred of the two major parties. In layman's terms, why waste your vote on a third-party candidate?
# Conclusion
If given the choice, I would prefer an instant-runoff voting system for both the Primary and General US Presidential Election. In the face of growing political polarization among Americans, I think an instant-runoff system would encourage more moderation. In addition, I think it would allow Americans who are affiliated most strongly with a party besides the two major ones to more sincerely express their preferences without worrying about strategic voting. Finally, I think the instant-runoff system would mitigate the "tyranny of the plurality."
