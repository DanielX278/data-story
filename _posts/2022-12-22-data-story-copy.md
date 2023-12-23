---
layout: post
title: "Data story"
author: "AMonAVIS"
---

The Man and the Machine focuses on this competition, comparing humans to an AI or algorithm constrained to behave like humans. We constrain the AI to have similar limitations as humans because without them, the AI could simply explore the entire Wikipedia network, find all the shortest paths, and store them. The algorithms we have designed to do so try to simulate rational human thinking. To compete under similar rules, the AI should only have access to the next neighbors of the directed graph, just like a human playing the game. Additionally, these AI won’t learn from the data, but will use predefined rules and logic to make decisions.

Our project starts from human navigation paths, defined by the users playing the Wikispeedia game. We then create a machine and analyze its navigation ability. We use several hand designed heuristics, and ask– how differently will these entities approach this same Wikispeedia game? Can machines get insights that we humans are not easily able to find? Will these machines be better than humans at playing the game?

Additionally, we have the advantage that this is a solved game. The shortest path between two nodes can be found exactly through methods like Dijkstra’s algorithm. This allows us to not only compare our solutions to human play, but also to the best possible answer.

## Research questions
* Which heuristics are best at predicting the shortest path in the Wikispeedia graph?
* Do any of these heuristics outperform humans?
* Can we identify any patterns or recurring structures in the human paths (ie. going for a central hub)?
* What insights can we draw from the cases where human paths outperform the machine ones? And vice versa?
* Is there any way to characterize these differences in performance? Categories, semantic distance, path length, or something else?
* How do these compare to the optimal path?

## Wikispeedia Graph

For this comparison, we are starting with the version of Wikispeedia that was created by \[4\]. Given a starting Wikipedia page, the goal is to reach a certain target Wikipedia page, only using the hyperlinks in the articles. Since the actual Wikipedia structure is huge, we worked with a subset of Wikipedia by using the provided Wikispeeda dataset. There are 4592 articles (the nodes with at least a connection in the graph) and 119882 hyperlinks (the edges).

# Study of the game 

To start our analysis, we investigate the information contained in the graph itself. This is also useful to exploit some of its properties to create more competitive machines. 

The first part of the game we analyze is the degree of the nodes. The degree of a node is equal to the number of edges going in or out of it. Plotting the Complementary Cumulative Distribution Function (CCDF) of the nodes' degrees, we can identify a Power Law and recognize how a small portion of nodes has an extremely high number of connections. In particular, we immediately detect how this phenomenon mostly concerns geopolitical or historical entities, like “Europe” and “France“ (with more than 1000 connections) or "World War II" (with close to 900  connections). At the same time, we identify in an interactive graph 13 nodes with just one connection, and 1581 nodes with a degree lower than 20. 

{% include Plotly_ccdf_plot.html %}

Let’s visualize what the nodes with the highest degree look like.

![HigherDegree60.png]({{ '/assets/images/HigherDegree60.png' | relative_url }})

We also want to find information about the PageRank of the nodes. This is because PageRank provides more detailed information than just the edge count, by allowing us to understand how well connected a node truly is to the rest.

PageRank computes a ranking of the nodes in the graph based on the structure of the incoming links. It was originally designed as an algorithm to rank web pages.

{% include Plotly_pageRank40_ordered.html %}
{% include Plotly_network_pagerank.html %}

# Study of Man 

## Path information

Out of 76193 games, we have 51318 finished games and 24875 unfinished ones. Of those, 47856 paths are unique. Data is collected until 2014, and the majority of games have been played in Q3 2009, with just 24% of them being completed sessions. With some further analysis, we looked for the categories of target articles with the highest counts of unfinished paths, and found that "Science", "Everyday life" and "Geography" are the three that most of the players seem to give up on. However, we must take into account that "Science" is the category with the highest number of articles, which means that we cannot conclude that the "Science" articles are the hardest to find in the game.

First, we want to get an idea of what are the most common finished, and unfinished paths. For starters, we’ll plot the target location only.

{% include Plotly_category_counts_finished.html %}
{% include Plotly_category_counts_unfinished.html %}

Between these two graphs, we can see that there are some differences in distribution. The actual frequency value should be ignored, as there are more finished than unfinished paths. It is interesting to note that it seems that geography is much more common in finished paths, this might be because geography nodes tend to be very common.

{% include Plotly_normalized_category_counts_finished.html %}
{% include Plotly_normalized_category_counts_unfinished.html %}

The following graph seems wrong, as the X axis is incredibly skewed. We checked in detail why this was happening, and found that there are people that are really stubborn while playing the game. In particular, one poor fellow visited 435 different pages trying to find a link between “United States” and “English Language”. Which is slightly unrelated, but is rather hilarious as a find. At the very least, the guy is stubborn and manages to find a link eventually.

{% include Plotly_distr_finished_path_length_humans.html %}

We redid the plot only taking in the players that took less than 25. This arbitrary value was chosen just for visualization purposes.

{% include Plotly_hist_human.html %}

## Good predictors of importance

In the original paper \[4\], the authors outline that humans give a priority to hubs when exploring which might not always be optimal. While this can be shown to be trivially true, it’s interesting to ask the following: Are hubs actually helpful for finding the shortest paths? Do humans use hubs?

We note that the original authors only describe hubs as those articles with a high degree. Is this the only definition of hub, or is there an alternative definition that is valid as well? We will investigate these elements.

First off, do humans actually use hubs? We will plot the appearance count, versus the degree of the nodes. For the count, we ignore a node if it is a source or a target node. Additionally, we only use finished paths.

{% include Plotly_appearances_in_paths_versus_degree.html %}

You can see there is some relation, and the fitted line gives high values. Does this hold up statistically? We did a regression analysis, finding the relation between count and degree. 

Based on the regression analysis, there is a correlation between how often a node is used in the paths and the degree. So this idea of players using a hub is definitely common for the human dataset. We obtained an adjusted R squared of 0.599, considering there is only one variable. So the degree does help explain how often it will appear in the paths.

Now, does this actually hold for the shortest paths?

Performing again a regression analysis and obtaining an adjusted R squared of 0.546, lower than before, we conclude that it is still quite high in this case. Most importantly, it also shows that there is a correlation between the degree and the number of times it appears in a shortest path! This implies that hubs are actually very helpful for finding out the path between two nodes, and are commonly used.

This is something that can be used in our AI, and will be taken into account.

Although we have already found that these hubs are actually very relevant, we want to explore if any other definitions are also valid. Namely, we want to see if the pagerank has an important correlation between the two values. After the regression analysis (Adj. R-squared: 0.616), we obtain that values are slightly higher between humans and PageRank, which is interesting.

![count_vs_degree.png]({{ '/assets/images/count_vs_degree.png' | relative_url }})

By comparison, PageRank has a slightly lower r squared score when it comes to the actual shortest path (0.507). This is a bit bizarre as PageRank has more info and is a more robust metric. It is still valid, it still has a high correlation and is still a decent explanation. But it is worse than just using the default degree. A possible explanation for this behavior is that Wikipedia articles (nodes) with a high PageRank are general topics, so it can be more challenging to find links to more specific topics, which are likely to be the target articles.

Another interesting point to consider is how important it is to approach similar topics to reach the target. To investigate this, we explore the semantic similarity between the target and the neighboring nodes (i.e. articles which have a connection to the target). This can give us insights on whether humans try to reach neighboring nodes with higher semantic similarity to approach the target, or if by the contrary, it they stick with topics with a high PageRank or node degree, and the cost of lower semantic similarity. We can then compare it to the shortest paths.

# Study of the Machine 

In this section, we will briefly go over the machines we implemented, and the logic behind them.

## Building machines

Based on the preliminary analysis and the research done by \[4\], the team decided to focus on using two things: exploiting semantic similarity between words, and exploiting graph properties. Based on these two ideas, we will be building our machines to compare with humans.

The first machine was inspired by the work done by \[5\], where they select some “landmark” nodes, and calculate the shortest path by estimating what the shortest path is using them. We use a simplification of it, picking landmark nodes as those with the highest number of edges, and directly assuming that all paths must go through the landmark nodes. We call this the Landmark approach.

The second approach tries to find a balance between the information we obtained from the PageRank and the semantic distance. For the semantic distance, we use this \[6\] sentence transformers model from hugging face, and for pagerank we use the built in networkX library. We call this method the SemanticRank approach.

The third approach works in a similar fashion as the second one. However, to compare what is a good trade-off between semantic similarity and PageRank, this third approach starts looking at semantic similarity earlier, while the second approach focuses on PageRank until the topic articles are more similar. We will call this method the PreSemanticRank approach.

## Limitations of exploration

For the exploration, we focused on the paths that have been finished by humans. Additionally, we restricted it to paths that have been completed at least 3 times. This ends up giving us 3860 different source target pairs to compare.

# Study of Man vs Machine 

The following plot shows the results for all the different categories. It shows the average path length per category:

{% include Plotly_avg_lengths_carlos.html %}


Based on this initial result, it’s clear that the landmark solution provides the best improvement overall, being consistently the lowest ranked. By comparison, while the semantic rank method was competitive with the average human, it never seemed to outperform.

This is actually interesting, as it hints at the idea that the underlying structure is the most relevant feature when exploring Wikispeedia, not the semantic analysis. We can make some intuitive argument for this, as the links that a page carries are not always relevant to the topic at its core. For example, United States might have a link to Copper, which is a link that is able to be exploited by one of the methods and not the other.

Also, the SemanticRank approach performs better than the PreSemanticRank approach in all categories, signifying the higher importance of navigating the important/hub nodes rather than trying to achieve before nodes with higher similarity. For this reason, we will not continue analyzing the PreSemanticRank approach in the subsequent analyses.


We will plot the histogram of the frequencies. We will plot the one for the human players as well, just to make it easier to read.

{% include Plotly_hist_landmark.html %}
{% include Plotly_hist_semrank.html %}

The first thing to note is that for the SemanticRank approach there is a peak at 25. This is because the method had a built-in system to force it to stop searching after that value. This was to make the method manageable to run.

The second thing to note is that, if you ignore this peak, the plots for the SemanticRank and the human data are actually relatively similar. You can notice a peak of around 5, and the fact that it’s a tailed distribution. The SemanticRank approach does have some holes around certain values, which is something we need to analyze a bit further. Nevertheless, this is interesting as it seems that the SemanticRank approach does seem to represent human thought, at least somewhat.

By comparison, the landmark approach is much more consistent, and has a more uniform distribution. It also has a higher amount of values around 4, not around 5. The reason behind this isn’t very clear, but it is interesting to note.

Now, how does this compare with the actual shortest path?

{% include Plotly_hist_shortest.html %}
This graph is rather similar to the landmark approach shortest path, but with there being more elements in 5 than in 3.

## Characterizing paths
Characterizing the paths taken by the methods could also lead to interesting results. Of course, this leads to the question, how do you characterize so many paths? Is there really any way of summarizing them?

Based on some research… no. At least, no easy way was found.

We did come up with an idea which is as follows: get the mean embedding of each element in the path, pass it through TSNE, and then plot it with the lengths. Ideally this provides some information, as TSNE was designed to keep some of the distance relations. We do lose some of the data of order, but no other simple alternative was found. We did the plots with the Landmark, PageRank, and the optimal values. These are the plots that we obtained:

{% include Plotly_optimal_tsne_length_plot.html %}
![tsne_landmark.png]({{ '/assets/images/tsne_landmark.png' | relative_url }})
![tsne_optimal.png]({{ '/assets/images/tsne_optimal.png' | relative_url }})
![tsne_semantic_rank.png]({{ '/assets/images/tsne_semantic_rank.png' | relative_url }})


This analysis is not particularly interesting for neither landmark, nor for the optimal value. It just resembles a blob of colors, nothing of note. This is interesting, as it means that the shortest path has almost nothing to do with semantic information. We have no way of characterizing path data from these methods, at least meaning wise.

The really interesting insight comes from the semantic embedding. We can notice that the elements that have a length of 24, which is the maximal allowed by this method, tend to be clustered together. It is also important to note that this is the mean embedding, so it is not an artifact of summing up the elements.

## One more thing
Can the machine do some of the unfinished paths that man couldn’t?

To test this, we only ran it for the Landmark algorithm. This was due to time constraints, as the others took much longer to process and would not allow proper answers. Additionally, we only took the unfinished paths where the type was ‘restart’. This is because these are the ones that the human explicitly gave up, the other half could just be that they left. As such, we don’t think they are interesting for comparison. Additionally, because there’s no path length to compare with humans, we only care if the system found a result or not. We also did a filtering step of only taking unique source/target combinations.

The end result was that the Landmark method had a 99.79% success rate in finding the paths that humans couldn’t!

We also investigated the paths that weren’t found, to see what the issue was. We did this by using the built-in NetworkX methods that find the shortest path. NetworkX was not able to find any of the paths either. This seems to imply that the Landmark algorithm was at least perfect in finding a path. But then, why are we given a possible path when there is none in reality?

We were able to distinguish the non-found paths into two types: One of the nodes doesn’t exist, or the path doesn’t exist. There were no cases where both nodes didn’t exist.

For nodes that didn’t exist, it was often the case where there was a typo in the node name. For example, the node was named ‘Usa’ instead of “United_States”. Other examples of this type that were found were ‘Adolph_Hitler’, ‘Long_peper’, ‘Great’. There were also nodes that just didn’t exist in the dataset.

The real interesting ones were the ones where no path was found whatsoever. So the nodes did exist in the graph. It was the following combinations: ('Anne_Frank', 'Final_Fantasy_%28video_game%29'),  ('Hymenoptera', 'Portuguese_Communist_Party'), and ('Oasis_%28band%29', 'Final_Fantasy_%28video_game%29').

To see the possible issues, we checked the outgoing nodes for the first element, and the incoming nodes for the second one. In the three cases, we found that the issue was with the incoming paths. In particular, 'Final_Fantasy_%28video_game%29’ and ‘Portuguese_Communist_Party’ only have a node that points towards them. For the case of 'Final_Fantasy_%28video_game%29’, this node has no nodes pointing towards it, and for ‘Portuguese_Communist_Party’, this node is only pointed to by ‘Portuguese_Communist_Party’ itself. So these nodes are for sure not reachable by anything else.

This means that our method was able to find at least some path connecting all elements, no matter what. Which is great!


## Future work
Due to time limits, we couldn’t do anything. There is also more work that can be done on doing additional research.

The most interesting thing to note is that our AI is limited. We came up with some solutions, but because this is a problem that can be expressed as an ML problem this problem can get as complicated as we want to. It might be that a better AI can use semantic distance.



# Conclusion

Is AI gonna take over our lives? Eh. Not our AI at least.

Jokes aside, we did find some interesting things. While the semantic rank method was not as competitive as humans, it provided plenty of interesting insight. The most relevant of this is that using semantic information is just not as important as understanding the graph, at least for the wikispeedia game. This is interesting, as it seems to be contrary to the idea of the way humans play by finding a hub and narrowing down the topic. At least, this approach is not optimal. This is also particularly bizarre, because this idea of hubs was actually found to be statistically relevant! What can explain this?

The answer is relatively simple: the fact of getting to a hub is important, the path towards the other node is not semantically important. The fact that semantic information can help us narrow down the search is probably not a strong relation, but is the only option we have. And it is helpful, at the end of the day the SemanticRank algorithm was able to find solutions.

We were not able to characterize paths in the case of the optimal shortest paths nor the landmark methods, but did find that shortest paths for the SemanticRank algorithm tend to cluster. This gives some intuition that the shortest path tends to have some common pitfalls, probably by falling into a similar path that leads to an inescapable solution.


## References

1)  "AlphaGo Movie". AlphaGo Movie. Archived from the original on 3 January 2018. Retrieved 14 October 2017.

2) Erly, A. (2021) The Original Chess Engine: Alan Turing’s Turochamp, Chess.com. Available at: https://www.chess.com/blog/the_real_greco/the-original-chess-engine-alan-turings-turochamp (Accessed: 21 December 2023).

3) Deep Blue (no date) IBM. Available at: https://www.ibm.com/history/deep-blue (Accessed: 21 December 2023).

4) "Wikispeedia: An Online Game for Inferring Semantic Distances between Concepts". Robert West, Joelle Pineau, and Doina Precup 21st International Joint Conference on Artificial Intelligence (IJCAI), 2009.

5) Potamias, Michalis, et al. "Fast shortest path distance estimation in large networks." Proceedings of the 18th ACM conference on Information and knowledge management. 2009.

6) https://huggingface.co/sentence-transformers/all-MiniLM-L6-v2

