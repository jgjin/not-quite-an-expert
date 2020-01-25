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
When we say that a group draws from a wider pool of knowledge and information than individual members, we can mean it in at least 2 ways:
1. Superadditive: the group gives a solution better than the best or average member.

For example, if you guess 24 and I guess 26, our average group guess is 25. If the actual value is 25, the group exhibited superadditive collective intelligence.

2. Emergent: the group gives a solution not possible by any individual member.

For example, if only you know how to obtain nails and only I know how to hammer nails, then together we can fix a table where we couldn't individually.
## A restricted solution space, continued
We might intuit that the council _should_ perform better together. However, given the previously described solution space and the definitions, the council cannot have superadditive nor emergent collective intelligence. Let us show why:

### Not superadditive
Let picking circle beris ("circle") be the better solution ("square" refers to picking square beris). Perhaps we know by contrivance that the mix of sun and rain this year has been especially kind to circle beri growth. Either:
1. Everyone votes "circle." The council's "circle" answer is as good as any member's "circle" answer.
2. Some, not all, people vote "circle."

    i. If the majority voted "circle," the council's "circle" answer is as good as the "circle" answer from the "average" member.[^1]

    ii. If the majority voted "square," the council's "square" answer is as good as the "square" answer from the "average" member (and worse than the "circle" answer from the wiser members).

3. Everyone votes "square." The group's "square" answer is as good as (i.e. as bad as) any member's "square" answer.

By the nature of the solution space and decision rule,[^2] in no situation can the council's answer be strictly better than the best or even average member's answer.
[^1]: By convention, we say that the "average" voter voted the majority option if there was a majority (rather than a tie or plurality).
[^2]: If you really think about it, any sensible decision rule would still result in the same property.
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

Together, the council arrives at the optimal 75% circle and 25% square picking proportions. The council exhibited superadditive (performing better than any individual member) collective intelligence. Since it was technically possible for a member to answer 75%/25%, the council did not exhibit emergent collective intelligence, by nature of the solution space.
# Conclusion
As we saw in this example, it is possible, regardless of the inherent characteristics of the problem, to restrict both superadditive and emergent collective intelligence depending on the solution space.
