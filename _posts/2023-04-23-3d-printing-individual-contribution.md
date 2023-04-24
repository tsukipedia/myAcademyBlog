---
title: "3D Printing"
date: 2023-04-23
---

# OVERVIEW

This technical log provides a detailed account of our team's experience in solving a problem that involved finding a printable color for three printers with varying ink levels in their cartridges. It covers the problem's context and requirements, our team's proposed algorithmic solution, and the challenges we faced during implementation. The log concludes with lessons learned from the experience, emphasizing the importance of effective communication.

# CONTEXT

The problem requires us to find a color that can be printed by all three printers with varying ink levels in their cartridges. The color is uniquely defined by 4 non-negative integers, c, m, y, and k, which indicate the number of ink units of cyan, magenta, yellow, and black ink needed to make the color. The total amount of ink needed to print a single logo is exactly 10^6 units. Given the number of units of ink each printer has in each cartridge, we need to output a color that can be printed by all three printers. If no such color exists, we output "IMPOSSIBLE".

The input consists of T, the number of test cases, followed by T test cases. Each test case consists of 3 lines, where each line contains 4 integers C(i), M(i), Y(i), and K(i), representing the number of ink units in the i-th printer's cartridge for the colors cyan, magenta, yellow, and black, respectively. The output should begin with "Case #x:" where x is the test case number (starting from 1), followed by either "IMPOSSIBLE" or 4 non-negative integers c, m, y, and k that add up to 10^6 and satisfy the constraints of the problem.

# SOLUTION

As a team, we came up with an algorithm to solve the problem during a call where we discussed the problem at hand and brainstormed possible solutions. After some casual discussion, we developed an algorithm that more formally consists of the following steps:

1. Initialize a variable for the test case counter.
2. For each test case, do the following:
  2.1. Read the ink levels for each of the three printers.
  2.2. Find the minimum ink level for each color (CMYK) across the three printers.
  2.3. Calculate the total ink level by summing the minimum ink levels for each color.
    2.3.1. Check if the total ink level is 10^6:
    2.3.2. If true, we have a valid result and no further calculations are needed.
    2.3.3. If false, adjust the ink levels as needed to ensure their sum is equal to 10^6:
      2.3.3.1. Calculate the difference between the total ink level and 10^6.
      2.3.3.2. Iterate through the minimum ink levels array and do the following:
      2.3.3.3. If the difference is greater than the current color ink level, subtract the color ink level from the difference and set the color ink level to 0.
      2.3.3.4. If the difference is less than the current color ink level, subtract the difference from the color ink level and break the loop.
    2.3.4. If it falls short, print "IMPOSSIBLE".
  2.4. Print the adjusted ink levels for each color (CMYK).

I began implementing the discussed solution in Python and managed to make it work faster than anticipated. I shared the progress with my partner, and we both agreed it was complete. However, our teammates soon pointed out that we had mistakenly used Python instead of the required Dart language. Upon realizing the error, I quickly translated the Python code to Dart, and it appeared to function correctly. However, when running it in the Google Code Jam compiler, we encountered a runtime error.

I discussed the issue with my partner, who informed me that he had done the same as me :facepalm: and encountered the same problem. We brought the issue to the rest of the team, and together we brainstormed potential causes and alternative approaches to resolving the problem. My partner Adrian was the one that determined that the issue stemmed from using a Dart method that was incompatible with the version of Dart used by the Google Code Jam compiler. Thankfully, our team meeting facilitated finding a solution to the problem. However, I must admit that the lack of communication throughout this process ultimately slowed us down and caused us to do double the work.
