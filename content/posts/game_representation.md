---
title: "Computing sequential games: representation"
date: 2022-04-09
draft: false
tags: ["programming", "technology"]
---
# Introduction
Let's solve a sequential game of complete and perfect information with computers! First, let's define such a game. Then, let's describe how to represent such a game with computers. Later, we'll describe an algorithm to solve such a game.
# Defining the game
A sequential game of complete and perfect information has 3 obvious features:
1. Sequential means players make moves separately in order
2. Complete information means players know everyone's outcomes
3. Perfect information means players know everyone's (possible and played) moves
# Representing the game
In mathematical notation, say we have _n_ players. Player _i_ (_i_ between 1 and n, inclusive) plays _i_-th in the sequence and has _c\_i_ choices, i.e. possible moves. We can represent all possible combinations of moves as _n_-tuples of the form (_m\_0_, _m\_1_, ..., _m\_n_), where _m\_i_ denotes the move the _i_-th player made and falls between 1 and _c\_i_, inclusive.

For example, if we have 3 players each with 3 possible moves, (1, 3, 2) means player 1 made their 1st possible move, then player 2 made their 3rd possible move, then player 3 made their 2nd possible move.

We represent outcomes as _n_-tuples as well: (_u\_1_, _u\_2_, ..., _u\_n_), where _u\_i_ denotes the utility, i.e. payoff, received by the _i_-th player. For example, (0.50, 3.75, 1.25) means player 1 got 0.50, player 2 got 3.75, and player 3 got 1.25 payoff.

To represent the game, we need to relate each possible combination of moves to its outcome. What if I framed each combination of moves as a "chord" of moves? Based on [my previous post, that framing points us toward tries]({{< ref "trie.md" >}}). Each vertex represents a combination of moves played so far. If all players have already played, the vertex contains the _n_-tuple representing the outcome. Otherwise, the vertex contains the possible moves for the current player.
# Conclusion
This trie represents the starting state of our game. In the next post, let's solve the game!
