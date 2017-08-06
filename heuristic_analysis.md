---
title: "Heuristic analysis of game agents in Isolation"
author: "Nicholas Chen"
output: pdf_document
---

# Explanation for evaluation functions

### Custom score 1: Balance of centrality and aggression

This custom score strikes a balance between the heuristics in custom score 2 and custom score 3 by summing these two scores together (see below for the definition of the second and third custom score). The aim is to allow each heuristic to reinforce each other (e.g. a weakness in custom score 2 will be balanced out by custom score 3 and vice-versa), since the second and third custom scores are aiming for different objectives.

### Custom score 2: Centrality measure

This custom score is a modification of `center_score`. While `center_score` encourages the current player to make moves which lead it away from the center, `custom_score_2` outputs the distance between the opponent and the center, subtracted by the distance between the player and the center. This encourages the player to make moves which eventually will cause the player to be close to the center, and opponent away from the center, reducing the possible moves for the opponent.

### Custom score 3: Improved score with increasing aggression

This custom score is a modification of `improved_score`. In `improved_score`, moves are chosen to maximize the available moves for the player and minimize the available moves for the opponent, but with an equal weighting. In this custom score, the weighting ('aggression') linearly increases as the game progresses, starting with a weighting of 1 (maximizing own moves and minimizing opponents moves are equally favored) and increasing to a weighting of 2 (minimizing opponents moves is twice as important as maximizing own moves) at the end of the game. This is made after observing that trapping the opponent is important during the endgame.

# Results
    
     Match #   Opponent    AB_Improved   AB_Custom   AB_Custom_2  AB_Custom_3 
                            Won | Lost   Won | Lost   Won | Lost   Won | Lost 
        1       Random      10  |   0    10  |   0     9  |   1    10  |   0  
        2       MM_Open      7  |   3     4  |   6     7  |   3     9  |   1  
        3      MM_Center    10  |   0     8  |   2     9  |   1    10  |   0  
        4     MM_Improved    6  |   4     8  |   2     9  |   1     6  |   4  
        5       AB_Open      5  |   5     4  |   6     2  |   8     6  |   4  
        6      AB_Center     7  |   3     5  |   5     3  |   7     5  |   5  
        7     AB_Improved    6  |   4     5  |   5     4  |   6     7  |   3  
    --------------------------------------------------------------------------
               Win Rate:      72.9%        62.9%        61.4%        75.7%    
               
# Discussion

While I expected that the first agent (`AB_Custom`) to be the best because it balanced both heuristics (found in `AB_Custom_2` and `AB_Custom_3`) which are logically sound, it seems from the results that the third agent (`AB_Custom_3`) is the winner by far. The data shows that:

* `AB_Custom_3` has the highest win rate among all the agents, and is the only agent to have a higher win rate than AB_Improved
* `AB_Custom_3` is the only agent that beats or draws against all other test agents
* `AB_Custom_3` is the only agent that beats `AB_Improved` (match number 7)

As such, `custom_score_3` should be used. 

Looking at `AB_Custom_2`, we observe that it beats the random and minimax test agents, but loses to all the alphabeta test agents. This might be due to the alphabeta pruning which causes the agent to search more effectively than the minimax test agents, however the centrality heuristic is intrinsically inferior, causing the agents to lose to the alphabeta test agents. The second agent has a lower win rate than the third agent in all matches except against `MM_Improved`, suggesting that the centrality heuristic is good against one scenario but lousy against most other scenarios.

Looking at `AB_Custom`, we see that all win rates are inferior to the higher of those by `AB_Custom_2` and `AB_Custom_3`, proving that my original hypothesis (that combining heuristics can help reinforce the weaknesses) is wrong in this case. Furthermore, combining the two heuristics can sometimes lead to a win rate that is worse than the lower of `AB_Custom_2` and `AB_Custom_3`, for example in the match against `MM_Center`. However, we should not let this result deter us from experimenting with combining heuristics: I have only tried a simple sum of the second and third evaluation functions, perhaps testing out variations of combinations (e.g. nonlinear combinations or combinations with non-equal weightings) might lead to a heuristic which is better than either the second or third heuristic alone.