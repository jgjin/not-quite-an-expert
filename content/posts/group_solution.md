---
title: "Restricting the solution space can restrict the benefits of group decision-making"
date: 2020-01-25
draft: false
tags: ["people", "groups", "management"]
---
# Introduction
For complex and difficult decisions, institutions often employ groups to make those decisions. For example, legal systems employ juries and companies employ hiring committees. Institutions prefer these groups because they are (hopefully) less likely to have inappropriate biases and because they draw from a wider pool of knowledge and information than individual members. Here's an interesting idea: no matter the characteristics of the problem, a restricted solution space can restrict these previously mentioned benefits. Let us consider an example.
# Beri Island
## Background
Beri Island is inhabited by a small, intimate group of farmers. The Island's main industry is beri-picking, as beris are plentiful on the island (hence the name). There are 2 types of beris: circle beris and square beris. Obviously, circle beris are more delicious and filling. However, they are harder to pick because they grow at higher altitudes and are harder to grasp. 
## A restricted solution space
The island summons a council of residents to decide whether to pick circle beris or square beris this year. Due to a recent environmental protection initiative (and population constraints), they are not allowed to fully harvest all of both beris.

The council makes decisions by majority vote. It considers the following solutions:
1. Pick the circle beris
2. Pick the square beris
## A brief intermission on collective intelligence
Formally, we could describe a problem (e.g. "generate some ideas for our next advertisement campaign" or "estimate the population of Lithuania"[^1]) and a solution mechanism (e.g. plurality vote, majority vote with runoffs, combination of individual solutions) in at least 2 ways.
1. **Superadditive**: the group gives a solution better than the best or average member. 
    Superadditive can be subdivided into 3 more precise versions:[^2]

    a. **Super-optimal:** the group gives a solution better than the best member.

    For example, if we were to generate a list of words ending with the letter s, our combined list would probably be better than the longer of our individual lists.

    b. **Super-expectative:** the expectation of the group's performance exceeds that of a randomly chosen individual's performance.

    For example, we are voting on choices A, B, or C for a multiple-choice question. We keep voting until we reach a consensus. For simplification, the voting rounds are statistically independent. You know B is wrong, so you will vote for choices A, B, and C with probability 0.70, 0.01, and 0.29, respectively. I know A is wrong, so I will vote for choices A, B, and C with probability 0.01, 0.70, and 0.29, respectively.
    
    Let's say C is the correct answer. Our probability of reaching consensus on C is roughly 0.86,[^3] which is greater than our individual probability of answering C of 0.29.

    c. **Super-pluralitive/super-majoritive:** the group's performance exceeds that which would result from blind, isolated plurality/majority vote.

    Note super-pluralitive applies mostly to discrete solution spaces. 
    
    Your friend, you, and I are deciding whether or not to grow fire carrots this year. You and I **love** fire carrots. If we had voted immediately, you and I would have the majority and head to the fire carrot seed store. However, your friend, an ecologist, convinces us to discuss the issue and points out that fire carrots explode above 100 degrees Fahrenheit. You, a weather enthusiast, know that there is a high chance temperatures will rise above 100 degrees this year, and you would rather not explode. After deliberation, we decide not to grow fire carrots, and we don't explode.

    We say a problem, solution mechanism, and solution space together are (regular) superadditive if at least 1 of the 3 versions is fulfilled and full superadditive if all 3 versions are fulfilled.[^4]

[^1]: Roughly 2.8 million
[^2]: Where did I get these words? I made them up, because they sounded right. I can do that! You can do that too!
[^3]: (0.29 * 0.29) / (0.29 * 0.29 + 0.70 * 0.01 + 0.01 * 0.70) = 0.857...
[^4]: Pending review by the Super-duper Official Commitee of Commitee of Commitees

2. Emergent: the group gives a solution not possible by any individual member.

    For example, if only you know how to obtain nails and only I know how to hammer nails, then together we can fix a table where we couldn't individually.
## A restricted solution space, continued
We might intuit that the council _should_ perform better together. However, given the previously described problem, solution mechanism (majority vote), and solution space, as well as the definitions of collective intelligence, the council cannot have full superadditive nor emergent collective intelligence. Let us show why:

### Not full superadditive
Let picking circle beris ("circle") be the better solution ("square" refers to picking square beris). Perhaps we know by contrivance that the mix of sun and rain this year has been especially kind to circle beri growth. Either:
1. Everyone votes "circle." The council's "circle" answer is as good as the full majority's "circle" answer.
2. Some, not all, people vote "circle."

    i. If the majority voted "circle," the council's "circle" answer is as good as the "circle" answer from the majority.

    ii. If the majority voted "square," the council's "square" answer is as good as the "square" answer from the majority.

3. Everyone votes "square." The group's "square" answer is as good as (i.e. as bad as) the majority's "square" answer.

By the nature of the solution space and solution mechanism,[^5] in no situation can the council's answer be strictly better than the majority. Therefore, the problem, solution mechanism. and solution space combination is not super-majoritive. By similar logic, it is not super-optimal either. Therefore, it cannot be full superadditive. However, it could be super-expectative (analogous to the example provided under the definition of super-expectative), so it could be regular superadditive.
[^5]: If you really think about it, any sensible decision rule would still result in the same property. Technically, if your solution mechanism was to vote the opposite of the majority and everyone voted "square," the council's "circle" answer implies the new solution mechanism enables the possibility of super-optimality.

### Not emergent
Any member can answer exactly one of "circle" or "square." The group can answer exactly one of "circle" or "square." Therefore, the council's solution space is the same as any member's individual solution space.
## A relaxed solution space
Suppose we relax the solution space so that we instead decide how much of the circle and square beris we should pick. The council knows from previous years that the residents of the island only have enough labor to pick proportion of circle beris + proportion of square beris <= 1. For instance, they could pick all of the circle beris, or 25% of the circle beris and 75% of the square beris, etc. After a round of extended discussion, the council will average their allocations to determine this year's picking proportions.

Even though the sun and rain have been kind to circle beris this year, as we pick more and more circle beris, they get harder to find and pick. Therefore, by contrivance we know that the optimal proportions are 75% circle beris and 25% square beris.

The 4 council members take a vote:
1. The first member recalls the recent weather. They votes for 100% circle and 0% square.
2. The second member recalls the weather as well. However, they has a fondness for square beris, recalling their grandmother's rich square beri soup recipe. They votes for 90% circle and 10% square.
3. The third member grew up picking circle beris, so knows that they get more difficult to pick as picking progresses. They votes for 60% circle and 40% square.
4. The final member absolutely **hates** circle beris. A few years ago, a mob of island residents angry at the harvest restrictions implemented to protect the island's ecosystem lobbed circle beris at them. They spent the rest of the day trying to get the stains out, and ended up throwing out the dress. Regardless, they still realize the weather has been kind to circle beris. They votes for 50% circle and 50% square.

Together, the council arrives at the optimal 75% circle and 25% square picking proportions. The council, under this new solution mechanism and solution space, exhibited full superadditive (performing better than any individual member) collective intelligence. Since it was technically possible for a member to answer 75%/25%, the council did not exhibit emergent collective intelligence.
# Conclusion
As we saw in this example, it is possible, regardless of the inherent characteristics of the problem, to restrict both full superadditive and emergent collective intelligence depending on the solution space.
