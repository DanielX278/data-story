---
layout: post
title: "Data story"
author: "Arthur Conan Doyle"
---

The Human and the Machine focuses on this, comparing an AI that has similar constraints to humans, and trying to find out how they compare. The project starts from human navigation paths, defined by the users playing the Wikispeedia game, to then dwell into the navigation ability of the machine, through semantic-distance-based paths: how differently will the two entities approach this same problem?

## What did we collect? (Exploratory data analysis)

We start from dataset that collected many data from users playing the Wikispeedia game. There, the goal is to reach a target Wikipedia page from a starting one, only using the hyperlinks in the articles. In particular, we didn't work with the entire Wikipedia network, but just a subset of it: we worked with 4592 articles (the nodes with at least a connection in the graph) and 119882 hyperlinks (the edges). 

Plotting the Complementary Cumulative Distribution Function (CCDF) of the nodes' degrees, we can identify a Power Law and recognize how a low portion of nodes has an extremely high number of connections. In particular, we immediately detect how this phenomenon mostly concerns geopolitical or historical entities, like "Europe" and "France" (with more than 1000 connections) or "World War II" (with morre than 500 and less than 1000 connections). At the same time, we identify 13 nodes with just one connection, and 1581 with a degree lower than 20.

<embed type="text/html" src="../assets/images/Plotly_distr_cat.html" width="500" height="200">
<iframe src="../assets/images/Plotly_distr_cat.html" width="100%" height="500px"></iframe>


[PageRank????oooooo]

Out of 76193 games, we have 51318 finished games and 24875 unfinished ones. Of those, 47856 paths are unique. Data is collected until 2014, and the majority of games has been played in Q3 2009, with just 24% of them being completed sessions. With some further analysis, we looked for the categories of target articles with the highest counts of unfinished paths, and found that "Science", "Everyday life" and "Geography" are the three that most of the players seem to give up on. However, we must take into account that "Science" is the category with the highest number of articles, which means that we cannot conclude that the "Science" articles are the hardest to find in the game.


- frequency of categories

## Something else

## Something more

## One more thing

