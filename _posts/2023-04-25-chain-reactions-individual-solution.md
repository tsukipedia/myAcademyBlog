---
category: Logs
title: Chain Reactions
date: 2023-04-25
---

# OVERVIEW
In this document, we introduce a problem that involves calculating the maximum fun a character named Wile can obtain by triggering chain reactions in a system of machines. The solution involves representing machines as tree nodes, modeling the entire chain of machines as a collection of tree nodes, and implementing a depth-first search algorithm to determine the best order to trigger the initiators.

# CONTEXT
This problem involves Wile, who lives alone in the desert and builds machines that run on chain reactions to have fun (very, very sad life). Each machine consists of N modules that point at each other in a certain order, and each module has a fun factor. Wile manually triggers initiator modules in some order, and each initiator triggers a chain reaction that may involve other modules with varying fun factors. Wile's overall fun from the session is the sum of the largest fun factor of all modules that triggered in each chain reaction. The goal is to compute the maximum fun Wile can have if he triggers the initiators in the best possible order, given the fun factors and setup of the modules. The input contains multiple test cases, where each test case includes the number of modules, the fun factors, and the setup of the modules. The output should contain the maximum fun Wile can have for each test case.
### Input
The first line of the input gives the number of test cases, T. T test cases follow, each described using 3 lines. Each test case starts with a line with a single integer N, the number of modules Wile has. The second line contains N integers F1, F2, …, FN where Fi is the fun factor of the i-th module. The third line contains N integers P1, P2, …PN. If Pi=0, that means module i points at the abyss. Otherwise, module i points at module Pi.
### Output
For each test case, output one line containing Case #x:y, where x is the test case number (starting from 1) and y is the maximum fun Wile can have by manually triggering the initiators in the best possible order.

# SOLUTION
To solve this problem, we need to represent each machine as a tree node, where each node stores its fun factor and a list of its children. We can then model the entire chain of machines as a collection of these tree nodes. The key is to implement a depth-first search (DFS) algorithm that calculates the sum of fun factors for each chain reaction initiated by Wile. Starting at each initiator (nodes pointing to the abyss), the DFS algorithm traverses through the children, seeking the chain with the lowest fun factor value. This chain should be triggered first, as it maximizes the fun Wile can obtain. The process is repeated for each initiator, selecting the optimal order in which to trigger them. The overall fun Wile gets from the session is the sum of the fun from each chain reaction, ensuring that the initiators are triggered in the best possible order to achieve maximum fun.

My partner and I solved this problem in python (the first approach). I must admit that my partner Adrian did it all by himself, my only job was to understand his solution to write this technical log.

Let's break down the code:

1. tree class:
-  Represents a tree node with an id, data (fun_factor), and a list of children.
- has_children method returns a boolean indicating if the node has children.
- __str__ method returns a string representation of the tree with indentation.
2. chain class:
- Represents a chain of trees. It contains a list of tree nodes, their last_id, and a list of nodes that are pointing to void (i.e., no parent).
- insert_machine method creates a new tree node and adds it as a child to the specified parent node or to the pointing_to_void list if it has no parent.
- __str__ method returns a string representation of the chain.
3. DFS_children function:
- A depth-first search algorithm that calculates the sum of fun factors in the tree, considering only the highest fun factor of each child tree and the lowest fun factor of the dead branches.
- It uses a list machines_watch_later as a stack for DFS, and pile_fun_to_compare as a stack to store fun factors of parent nodes and their children.
4. DFS function:
- Iterates over the trees in the pointing_to_void list of the chain and calculates the sum of fun factors using the DFS_children function.
read_chain_from_input function:
- Reads input from the user to create a chain.
- The number of machines (trees), fun factors, and connections are read from the user's input.
5. main function:
- Reads the number of test cases and executes the program for each test case.
- For each test case, it reads the chain from input, calculates the sum of fun factors using the DFS function, and prints the result.
