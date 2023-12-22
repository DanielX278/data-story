---
layout: post
title: "NEWWWW Data story"
author: "AMonAVIS"
---
# Graph storm

{% include Plotly_network_pagerank.html %}
{% include Plotly_man_len_avgs.html %}
{% include Plotly_finished_paths_categories.html %}
![histogram_pagerank.png]({{ '/assets/images/histogram_pagerank.png' | relative_url }})
![tsne_landmark.png]({{ '/assets/images/tsne_landmark.png' | relative_url }})
{% include Plotly_appearances_in_paths_versus_degree.html %}
![PageRankTop80.png]({{ '/assets/images/PageRankTop80.png' | relative_url }})
![count_vs_degree.png]({{ '/assets/images/count_vs_degree.png' | relative_url }})
{% include Plotly_network_higher_degree.html %}
{% include Plotly_avg_lengths.html %}
{% include Plotly_distr_finished_path_length_25_humans.html %}
![PageRankAboveMedian.png]({{ '/assets/images/PageRankAboveMedian.png' | relative_url }})
{% include Plotly_normalized_category_counts_unfinished.html %}
![perfect_count_vs_degree.png]({{ '/assets/images/perfect_count_vs_degree.png' | relative_url }})
![histogram_shortest_optimal.png]({{ '/assets/images/histogram_shortest_optimal.png' | relative_url }})
{% include Plotly_Average_Finished_Path_Length_by_Category_of_Machine_vs_Man.html %}
![histogram_landmark.png]({{ '/assets/images/histogram_landmark.png' | relative_url }})
{% include Plotly_distr_finished_path_length_humans.html %}
{% include Plotly_category_counts_unfinished.html %}
![tsne_semantic_rank.png]({{ '/assets/images/tsne_semantic_rank.png' | relative_url }})
{% include Plotly_category_counts_finished.html %}
![DegreeTop.png]({{ '/assets/images/DegreeTop.png' | relative_url }})
{% include Plotlu_distr_cat.html %}
![HigherDegree60.png]({{ '/assets/images/HigherDegree60.png' | relative_url }})
![perfect_count_vs_pagerank.png]({{ '/assets/images/perfect_count_vs_pagerank.png' | relative_url }})
{% include Plotly_unfinished_paths_categories.html %}
{% include Plotly_counts_graph.html %}
![PageRankClassificationAll.png]({{ '/assets/images/PageRankClassificationAll.png' | relative_url }})
{% include Plotly_normalized_category_counts_finished.html %}
{% include Plotly_man_v_mac.html %}
{% include Plotly_PageRank40.html %}
![human_count_vs_degree.png]({{ '/assets/images/human_count_vs_degree.png' | relative_url }})
![AveragePageRank60.png]({{ '/assets/images/AveragePageRank60.png' | relative_url }})
{% include Plotly_distr_cat.html %}
![PageRankArtAboveAverage.png]({{ '/assets/images/PageRankArtAboveAverage.png' | relative_url }})
![histogram_human_lengths.png]({{ '/assets/images/histogram_human_lengths.png' | relative_url }})
![tsne_optimal.png]({{ '/assets/images/tsne_optimal.png' | relative_url }})
![human_pagerank_vs_count.png]({{ '/assets/images/human_pagerank_vs_count.png' | relative_url }})



The Man and the Machine focuses on this competition, comparing humans to an AI constrained to behave like humans. We constrain the AI to have similar limitations as humans because without them, the AI could simply explore the dataset, find all shortest paths, and store them.

Our project starts from human navigation paths, defined by the users playing the Wikispeedia game. We then create a machine and analyze its navigation ability. We use several hand designed heuristics, and ask– how differently will these entities approach this same Wikispeedia game? Can machines get insights that we humans are not easily able to find? Will these machines be better than humans at playing the game?

Additionally, we have the advantage that this is a solved game. The shortest path between two nodes can be found exactly through methods like Dijkstra’s algorithm. This allows us to not only compare our solutions to human play, but also to the best possible answer.


## Research questions
* Which heuristics are best at predicting the shortest path in the Wikispeedia graph?
* Do any of these heuristics outperform humans?
* Can we identify any patterns or recurring structures in the human paths (ie. going for a central hub)?
* What insights can we draw from the cases where human paths outperform the machine ones? And vice versa?
* Is there any way to characterize these differences in performance? Categories, semantic distance, path length, or something else?
* How do these compare to the optimal path?




# Study of the game 

## Wikispeedia Graph

For this comparison, we are starting with the version of Wikispeedia that was created by <sup>4</sup>. Given a starting Wikipedia page, the goal is to reach a certain target Wikipedia page, only using the hyperlinks in the articles. Since the actual Wikipedia structure is huge, we worked with a subset of Wikipedia by using the provided Wikispeeda dataset. There are 4592 articles (the nodes with at least a connection in the graph) and 119882 hyperlinks (the edges).

To start our analysis, we investigate the information contained in the graph itself. This is also useful to exploit some of its properties to create more competitive machines. 

The first part of the game we analyze is the degree of the nodes. The degree of a node is equal to the number of edges going in or out of it. Plotting the Complementary Cumulative Distribution Function (CCDF) of the nodes' degrees, we can identify a Power Law and recognize how a small portion of nodes has an extremely high number of connections. In particular, we immediately detect how this phenomenon mostly concerns geopolitical or historical entities, like “Europe” and “France“ (with more than 1000 connections) or "World War II" (with close to 900  connections). At the same time, we identify 13 nodes with just one connection, and 1581 nodes with a degree lower than 20.



We also want to find information about the PageRank of the nodes. This is because PageRank provides more detailed information than just the edge count, by allowing us to understand how well connected a node truly is to the rest.

PageRank computes a ranking of the nodes in the graph based on the structure of the incoming links. It was originally designed as an algorithm to rank web pages.

{% include Plotly_PageRank40.html %}
{% include Plotly_network_pagerank.html %}





# Study of Man 

## Path information

Out of 76193 games, we have 51318 finished games and 24875 unfinished ones. Of those, 47856 paths are unique. Data is collected until 2014, and the majority of games has been played in Q3 2009, with just 24% of them being completed sessions. With some further analysis, we looked for the categories of target articles with the highest counts of unfinished paths, and found that "Science", "Everyday life" and "Geography" are the three that most of the players seem to give up on. However, we must take into account that "Science" is the category with the highest number of articles, which means that we cannot conclude that the "Science" articles are the hardest to find in the game.

First, we want to get an idea of what are the most common finished, and unfinished paths. For starters, we’ll plot the target location only.



Between these two graphs, we can see that there are some differences in distribution. The actual frequency value should be ignored, as there are more finished than unfinished paths. It is interesting to note that it seems that geography is much more common in finished paths, this might be because geography nodes tend to be very common.


This graph seems wrong, as the X axis is incredibly skewed. We checked in detail why this was happening, and found that there are people that are really stubborn while playing the game. In particular, one poor fellow visited 435 different pages trying to find a link between “United States” and “English Language”. Which is slightly unrelated, but is rather hilarious as a find. At the very least, the guy is stubborn and manages to find a link eventually.

We redid the plot only taking in the players that took less than 25. This arbitrary value was chosen just for visualization purposes.




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


