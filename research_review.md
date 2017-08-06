---
title: "Research review: Mastering the game of Go with deep neural networks and tree search"
author: "Nicholas Chen"
output: html_document
---

# Introduction

In this paper, a new approach to computer Go is introduced. It uses value networks to evaluate board positions and policy networks to select moves. 'Networks' in this context refers to deep neural networks, which is an input/output function. These deep neural networks are trained by a combination of supervised learning based on human expert games and reinforcement learning from games of self-play. They combined Monte Carlo tree search (MCTS) with the value and policy networds to achieve a 99.8% winning rate against other Go programs, and defeated Fan Hui, the human European Go champion, by 5-0. This is a significant event because a computer program has defeated a human professional player for the first time, a feat that was originally thought to be at least a decade away.

# Goals

Their main goal is to search through moves efficiently and effectively --  Go has a branching factor of $b \approx 250$ and depth of $d \approx 150$, making exhaustive search infeasible. In comparison, for chess, $b \approx 35$ and depth of $d \approx 80$. It is the extremely large search space $b^d$ of Go which led experts to estimate that it would take at least another decade before computers beat professional humans in this game.
 
# Techniques introduced

Main contribution is to combine value and policy networks with MCTS

Previous programs used shallow policies or handcrafted value functions (often based on a linear combination of features). AlphaGo uses value networks in place of handcrafted value functions.

### Monte Carlo tree search (MCTS)

Monte Carlo tree search (MCTS) is the main search algorithm to estimate the value of each state in the search tree. MCTS consists of 4 steps:

* Selection, where successive child nodes of a root node are selected till a leaf node is selected. 
* Expansion, where new child node(s) are created and one of them is selected.
* Simulation, where a search is conducted to the end without branching by sampling actions for both players from a policy (a.k.a Monte Carlo rollout). 
* Backpropagation, where the result of the rollout is used to update values from the child node upwards till the root node.

The policy and value networks are of the convolutional neural network type, which is used commonly in image analysis. The input to the networks is the board represented as a 19 X 19 image. The effective breadth and depth of the search tree are reduced by evaluating positions using a value network and sampling actions using a policy network.

### Training of neural networks

The policy network is first trained via supervised learning, where the inputs are expert human moves from the KGS Go Server and the output is the probability distribution over legal moves. Also, they trained a fast policy network which is less accurate but returns the output faster (by a factor of ~1000).

Next, they initialized a reinforcement learning policy network with the supervised learning policy network from above, and trained the reinforcement learning policy network via games of self-play and optimizing for the final outcome .

Lastly, they train a value network that predicts the winner of games played by the reinforcement learning policy network against itself.

AlphaGo efficiently combines the value and policy networks with MCTS.

# Results

The supervised learning policy network was able to predict expert moves with an accuracy of 57.0% compared to the state-of-the-art accuracy of 44.4%.

The reinforcement learning policy network won 85% of games against Pachi, the strongest open-source Go program, without using any search techniques.