---
layout: post
title: "NEWWWW Data story"
author: "Arthur Conan Doyle"
---

The Human and the Machine focuses on this, comparing an AI that has similar constraints to humans, and trying to find out how they compare. These constraints are created to force the AI to have similar limitations as us, as without them it could just explore the dataset, find all shortest paths, and just reuse them.

The project starts from human navigation paths, defined by the users playing the Wikispeedia game, to then dwell into the navigation ability of the machine, through different heuristics that are hand designed: how differently will these entities approach this same problem? Can machines get insights that we humans are not easily able to find? Will these machines be better than humans at playing the game?

We additionally have the advantage that this is a solved game. The shortest path between two nodes can be found exactly through methods like Dijkstra’s algorithm. This allows us to not only compare our solutions to human play, but also to the best possible answer.

## Research questions
* Which heuristics are better at predicting the shortest path in the Wikispeedia graph?
* Do any of these heuristics come on top?
* Can we identify any patterns or recurring structures in the human paths (ie. going for a central hub)?
* What insights can we draw from the cases where human paths outperform the machine ones? And vice versa?
* Is there any way to characterize these differences in performance? Categories, semantic distance, path length, or something else?
* How do these compare to the optimal path?

# Graph storm

prova 

{% include Plotly_network_pagerank.html %}
{% include Plotly_man_len_avgs.html %}
{% include Plotly_finished_paths_categories.html %}
{% include Plotly_network_higher_degree.html %}
{% include Plotlu_distr_cat.html %}
{% include Plotly_unfinished_paths_categories.html %}
{% include Plotly_man_v_mac.html %}


# Study of the game 

## Wikispeedia Graph

For this comparison, we are starting with the version of Wikispeedia that was created by <sup>4</sup>. There, the goal is to reach a target Wikipedia page from a starting one, only using the hyperlinks in the articles. This meant we worked with a subset of Wikipedia: there were 4592 articles (the nodes with at least a connection in the graph) and 119882 hyperlinks (the edges).

For starters, we will work on analyzing the information contained in the graph, to come up with ideas on how we can try and exploit some of its properties to create more competitive machines. 

The degree of a node is equal to the number of edges going in or out of it. Plotting the Complementary Cumulative Distribution Function (CCDF) of the nodes' degrees, we can identify a Power Law and recognize how a small portion of nodes has an extremely high number of connections. In particular, we immediately detect how this phenomenon mostly concerns geopolitical or historical entities, like “Europe” and “France“ (with more than 1000 connections) or "World War II" (with more than 500 and less than 1000 connections). At the same time, we identify 13 nodes with just one connection, and 1581 nodes with a degree lower than 20.


{% include Plotly_distr_cat.html %}

We also want to find information about the PageRank of the nodes. This is because PageRank provides more detailed information than just the edge count, by allowing us to understand how well connected a node truly is to the rest.


Figures/Plotly_PageRank40.html



# Study of Man 

## Path information

Out of 76193 games, we have 51318 finished games and 24875 unfinished ones. Of those, 47856 paths are unique. Data is collected until 2014, and the majority of games has been played in Q3 2009, with just 24% of them being completed sessions. With some further analysis, we looked for the categories of target articles with the highest counts of unfinished paths, and found that "Science", "Everyday life" and "Geography" are the three that most of the players seem to give up on. However, we must take into account that "Science" is the category with the highest number of articles, which means that we cannot conclude that the "Science" articles are the hardest to find in the game.

First, we want to get an idea of what are the most common finished, and unfinished paths. For starters, we’ll plot the target location only.




Between these two graphs, we can see that there are some differences in distribution. The actual frequency value should be ignored, as there are more finished than unfinished paths. It is interesting to note that it seems that geography is much more common in finished paths, this might be because geography nodes tend to be very common.





% More graph properties here, tbd


- frequency of categories

## Good predictors of importance
In the original paper <sup>4</sup>, the authors outline that humans give a priority to hubs when exploring which might not always be optimal. While this can be shown to be trivially true, it’s interesting to ask the following: Are hubs actually helpful for finding the shortest paths? Do humans use hubs?

We note that the original authors only describe hubs as those articles with a high degree. Is this the only definition of hub, or is there an alternative definition that is valid as well? We will investigate these elements.

First off, do humans actually use hubs? We will plot the appearance count, versus the degree of the nodes. For the count, we ignore a node if it is a source or a target node. Additionally, we only use finished paths.


You can see there is some relation, and the fitted line gives high values. Does this hold up statistically? We will do a regression analysis, finding the relation between count and degree. These are the results we obtain:

Based on the regression analysis, there is a correlation between how often a node is used in the paths and the degree. So this idea of players using a hub is definitely common for the human dataset.

We can also see that there is a high R squared, considering there is only one variable. So the degree does help explain how often it will appear in the paths.

Now, does this actually hold for the shortest paths?


While the R squared is lower, it is still quite high in this case. Most importantly, it also shows that there is a correlation between the degree and the number of times it appears in a shortest path! This implies that hubs are actually very helpful for finding out the path between two nodes, and are commonly used.

This is something that can be used in our AI, and will be taken into account.

Although we have already found that these hubs are actually very relevant, we want to explore if any other definitions are also valid. Namely, we want to see if the pagerank has an important correlation between the two values.





# Study of the Machine 
## Building machines

Based on the preliminary analysis and the research done by <sup>4</sup>, the team decided to focus on using two things: exploiting semantic similarity between words, and exploiting graph properties. Based on these two ideas, we will be building our machines to compare with humans.

The first machine was inspired by the work done by <sup>5</sup>, where they select some “landmark” nodes, and calculate the shortest path by estimating what the shortest path is using them. We use a simplification of it, picking landmark nodes as those with the highest number of edges, and directly assuming that all paths must go through the landmark nodes. We call this the Landmark approach.

The second approach tries to find a balance between the information we obtained from the pagerank and the semantic distance. For the semantic distance, we use this <sup>6</sup> sentence transformers model from hugging face, and for pagerank we use the built in networkX library. We call this method the SemanticRank approach.

## Limitations of exploration

For the exploration, we focused on the paths that have been finished by humans. Additionally, we restricted it to paths that have been completed at least 3 times. This ends up giving us 3860 different source target pairs to compare.



# Study of Man vs Machine 


## One more thing
Can the machine do some of the unfinished paths that man couldn’t?
Out of the unfinished human games that the Landmark algorithm attempted, the Landmark algorithm was able to complete 100% of the games
Out of the unfinished human games that the Semantic Rank algorithm attempted, it was able to complete 93.0 % of the games.

## References
1)  "AlphaGo Movie". AlphaGo Movie. Archived from the original on 3 January 2018. Retrieved 14 October 2017.

2) Erly, A. (2021) The Original Chess Engine: Alan Turing’s Turochamp, Chess.com. Available at: https://www.chess.com/blog/the_real_greco/the-original-chess-engine-alan-turings-turochamp (Accessed: 21 December 2023).

3) Deep Blue (no date) IBM. Available at: https://www.ibm.com/history/deep-blue (Accessed: 21 December 2023).

4) "Wikispeedia: An Online Game for Inferring Semantic Distances between Concepts". Robert West, Joelle Pineau, and Doina Precup 21st International Joint Conference on Artificial Intelligence (IJCAI), 2009.

5) Potamias, Michalis, et al. "Fast shortest path distance estimation in large networks." Proceedings of the 18th ACM conference on Information and knowledge management. 2009.

6) https://huggingface.co/sentence-transformers/all-MiniLM-L6-v2


