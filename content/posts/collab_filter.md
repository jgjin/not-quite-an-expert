---
title: "Collaborative filtering"
date: 2020-04-11
draft: false
tags: ["people", "groups", "technology"]
---
# Introduction
Collaborative filtering is an important problem in recommender systems. These recommender systems form the basis of some of the largest products today: search (Google), marketplace (Amazon), and content (Netflix, YouTube) products all rely on some form of recommender system.
# Collaborative filtering types
Collaborative filtering comes in 2 main types: memory-based and model-based. Memory-based models employ user and item data, described in detail in the next section, to form recommendations. Model-based techniques, outside the scope of this post, encompass all other deep (neural network) and shallow models used to form recommendations.
# Memory-based collaborative filtering data 
With memory-based techniques, we have a dataset of users and items. Though not always, users are usually human users, and items are things they consume or use. These items differ in relevance or utility. Our goal is to recommend to the user the most relevant or useful items.

Typically, the dataset is described as a matrix, where users are rows and items are columns, or users are columns and items are rows. The value in a cell is the user's "value" for that item. This "value" could be one of many things:
1. Whether the user consumed the item (binary 1/0)
2. How intensely the user consumed the item (e.g. watch time)
3. How the user rated the item

To form a recommendation, we want to predict the items which would have the highest value to a user. That is, the one they would most likely consume, consume the most, or rate the highest, according to the three types of "values" above.

A note on convention: we call the user for whom we are currently recommending the "focal user" or the "active user." For simplicity, we assume users are rows and items are columns for the rest of the post. 
# User-based collaborative filtering
User-based collaborative filtering compares users to form recommendations. Also known as user-user collaborative filtering, the process involves:
1. Calculate the similarity of every other user to the focal user.
2. Take the top _k_ most similar users.
3. Use those top _k_ most similar users, weighted by their similarity, to predict values not yet known for the focal user.

Hold on, what is _k_? And what do we mean by "similarity"? _k_ is a hyperparameter. In plain English, you get to choose k. In practice, you would test many _k_ values and pick the best-performing value for _k_. The "similarity" is also a hyperparameter. Types of similarity you could use include:
1. Pearson correlation: widely understood
2. Cosine similarity: handles zero values gracefully, unlike correlation
3. Anything else that is defined between two vectors

Since each row represents a user, you would calculate the similarity on the focal user's row (vector) and the other user's row (vector).
# Problems with user-based collaborative filtering
User-based collaborative filtering in this basic form is rarely used in practice. Problems include:
1. User-item datasets are sparse. That is, users tend to use or rate very few of the many items available.
2. Computing all user similarity values is expensive. For u users, there are (u)(u - 1) / 2 pairs of users, which grows quickly. Only a few thousand users leads to millions of pairs. Often, there are many more users than items.
3. User profiles change quickly. Users consume new items all the time, so user similarity needs to be recomputed often.
# Item-based collaborative filtering
Invented by Amazon in 1998, item-based filtering compares items to form recommendations. Also known as item-item collaborative filtering, the process very similar:
1. Calculate the similarity of every item to every other item.
2. When the focal user consumes an item, add the top _k_ most similar items to their recommendations.

You can use pretty much the same measures of similarity as in user-based collaborative filtering. Instead of taking the rows, you would take the columns, which correspond to each item, to calculate similarity.
# Advantages of item-based collaborative filtering
Advantages item-based collaborative filtering has over user-based collaborative filtering include
1. Items tend to be more stable than users. Items tend to have many values compared to users, so item similarities do not need to be recomputed as often.
2. Because of that, item-item similarities can be computed offline. That is, they can be computed ahead of time without waiting for the focal user to consume an item.
# Applications
Because of these advantages, item-based collaborative filtering is used by Amazon and YouTube. I could not find any practical examples of user-based collaborative filtering besides tutorials on how to perform it on well-known or toy datasets. However, user-based collaborative filtering could be advantageous if users are fewer and more stable than items.

Obviously, the recommendation systems used by Amazon and YouTube are much more sophisticated than what I described here. They may use techniques to collapse (look up matrix decomposition) or otherwise handle sparse matrices, and almost certainly use some form of machine learning to aid the recommendation.
# Conclusion
Let's end with some caveats on collaborative filtering:
1. Collaborative filtering is nearly useless without rich (i.e. a lot of) user-item data. It may not be feasible with entirely new products without user-item data.
2. Collaborative filtering tends to recommend what is already popular. An item was popular, so it gets recommended often, so it stays popular. There are some techniques to handle the bias of recommender systems toward already-popular items. However, the easiest fix is often human intervention. For example, Spotify uses human-curated playlists, and Amazon has an Onsite Associates Program, which uses people to generate best product guides to show to customers.
3. In some cases, users may prefer a "human touch" to brighten their customer experience. This usually applies to higher-end and luxury products and services.
