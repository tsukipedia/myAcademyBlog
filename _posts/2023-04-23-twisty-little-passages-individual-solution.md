---
title: Twisty Little Passages
date: 2023-04-26
---

# OVERVIEW
In this document, you will find a detailed explanation of a cave exploration problem and its solution. The problem involves estimating the number of passages within a cave system, given a limited number of operations. We provide context for the problem, including its requirements, and present our solution approach, which involves teleporting to a random subset of rooms and using their average degree to estimate the total number of passages. 

# CONTEXT
This problem involves exploring a cave with N rooms connected by bidirectional passages. The goal is to estimate the number of passages in the cave with at most K operations, where each operation is either teleporting to a room of your choice or walking through a random passage connected to the current room. The program reads T test cases, where each test case includes the number of rooms and maximum number of operations allowed, and then processes up to K+1 exchanges. Each exchange involves identifying the current room and its number of passages, and outputting a W to walk through a random passage, a T and a room number to teleport, or an E and an estimate of the number of passages to finish exploring. The program must output an estimate within a range of 2/3 to 4/3 of the actual number of passages to be considered correct, and must pass at least 90% of the test cases in a set to pass that set. The judge will print -1 and exit if the program sends an invalidly formatted line or if the (K+1)-th exchange is not an estimation operation.

# SOLUTION
To be honest, I didn't participate in writing the solution for this problem, but it was because the solution was relatively straightforward rather than due to my unavailability. However, we did encounter some difficulties with the Google Code Jam compiler when trying to run our Python solution. Although we had no issues using the downloadable testing tool, we were unable to get the solution to run in the compiler. As a team, we worked together to resolve the problem. Once the compilation issue was resolved, my partner planned to translate the working Python solution into TypeScript but soon enough another couple decided to take over the responsibility of implementing the solution in TypeScript.

As for the solution itself, the problem description provided a suggested approach that we found easy to understand and implement. The basic idea was to teleport to a random subset of rooms, calculate the average degree of those rooms, and use that as an estimate of the average degree of all rooms. Since the number of passages is half the sum of the room degrees, we could then estimate the total number of passages in the cave. Our code followed this algorithm:

Based on that description, we needed to make a code that follows the flow of the next algorithm:
1. Read the number of test cases.
2. For each test case, do the following:
    1. Read the number of rooms, number of actions, initial room, and number of passages.
    2. Create a list of unseen rooms, excluding the initial room.
    3. Shuffle the unseen rooms list and select the first K rooms (based on the number of actions).
    4. For each selected room, teleport to the room and read the current room and number of passages, then store the number of passages in a list.
    5. Calculate the estimated total number of passages by multiplying the sum of the passages seen by the total number of rooms, and divide by 2 times the length of the passages seen list.
    6. Print the estimated total number of passages.

