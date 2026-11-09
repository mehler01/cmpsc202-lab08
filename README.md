# Lab 08: Navigating Weighted Graphs with Dijkstra's Algorithm

Name(s): Josh Mehler  

## Introduction
You are already familiar with Breadth-First Search (BFS) and Depth-First Search (DFS) for exploring graphs. However, what happens when all edges are not created equal? In this lab, we will explore Dijkstra's Algorithm, which allows us to find the shortest path between two vertices in a *weighted* graph.

---

## Part 1: BFS with Weights

BFS is excellent for finding the shortest path in an unweighted graph. Let's see why it fails when weights are introduced. Consider the following weighted graph:

![Part 1 Graph](./part1.png)

**Questions:**
1. If you run a standard BFS starting at Node $A$, looking for Node $D$, which path will the BFS algorithm discover first? Why?
   > The path that BFS would determine would be directly from A to D, since D is a nearest neighbor to A. Going directly from A to D is the shortest path in terms of the number of "jumps".

2. What is the total weight of the path BFS found? What is the total weight of the alternative path? 
   > The total weight of the BFS path is 100, because it is just the path from A to D. THe alternate path, A to B to C to D, has a total weight of 9.

3. How does the definition of "shortest path" change when moving from unweighted graphs to weighted graphs?
   > The shortest path is no longer the path that requires the least amount of "jumps", but instead it is the path that incurrs the lowest weight cost.

---

## Part 2: Dijkstra's Algorithm

Dijkstra's algorithm uses a greedy approach. It maintains a set of "visited" (finalized) nodes and an array of the shortest known distances to all other nodes. Consider the following weighted graph:

![Part 2 Graph](./part2.png)

**Instructions:**
Starting at Node $A$, trace Dijkstra's algorithm. Fill in the tracker table below. 
*   **Current Node:** The unvisited node with the smallest current distance.
*   **Neighbors to Check:** The neighbors of the current node whose distances might need updating.
*   **`dist` Array:** The current known shortest distance from $A$ to each node. Use $\infty$ for unreachable/unknown nodes.
*   **`visited` Set:** The set of nodes that have been finalized and whose shortest distances are known.

| Step | Current Node | Neighbors to Check | `dist` Map | `visited` Set | 
| :--- | :--- | :--- | :--- | :--- | 
| 0 | $A$ | $B$, $C$ | $A$:0, $B$:4, $C$:2 | { } | 
| 1 | $C$ | $B$, $D$, $E$ | $A$:0, $B$:3, $C$:2, $D$:10, $E$:12 | {C} | 
| 2 | $B$ | $D$ | $A$:0, $B$:3, $C$:2, $D:8$, $E$:12| {C,B} | 
| 3 | $D$ | $E$ | $A$:0, $B$:3, $C$:2, $D$:8, $E:10$ | {C,B,D,E} | 
| 4 | $E$ | | $A$:0, $B$:3, $C$:2, $D$:8, $E:10$ | {C,B,D,E} | 

**Conceptual Question:**
When we add a vertex to the "visited" set, why are we 100% certain we have found the absolute shortest path to that node? 
> The algorithm will always move to the nearest n      neighbor from the current node, so we are using a greedy approach to always take the shortest step to each node. 

---

## Part 3: Assumptions

Dijkstra's algorithm relies on an assumption about the weights. Let's see what happens when we break it. Consider the following weighted graph, which contains a negative edge:

![Part 3 Graph](./part3.png)

**Instructions:**
Trace Dijkstra's algorithm starting from Node $A$ to find the shortest path to Node $C$.

1. Which node does Dijkstra's algorithm visit first after $A$?
   > Dijkstra's algorithm will visit node C first after A, because it has the shortest path to A (in the updated example).

2. What will Dijkstra's algorithm declare as the shortest path to $C$, and what is its weight?
   > Dijkstra's algorithm will declare that the shortest path is from A to C is directly from A to C with weight 1. This is because it is using a greedy approach, and assuming the distance between B and C is positive.

3. Look at the graph manually. What is the *actual* shortest path from $A$ to $C$, and what is its total weight? 
   > The actual shortest distsance is from A to B to C, which has a weight of -3.

4. **The Trap:** Explain exactly *why* Dijkstra's algorithm failed to find the correct shortest path in this scenario.
   > It failed to catch this path, because it uses a greedy approach, and assumes that the path between B and C is positive.

---

## Part 4: Implementing Dijkstra's Algorithm

In the real world, problems rarely look like neat mathematical graphs right away. You often have to translate real-world constraints into a standard graph format so that an algorithm like Dijkstra's can process it.

**The Scenario:**
You are building a navigation app for delivery robots on a college campus. 

* Moving between any two adjacent sidewalk intersections takes exactly $2$ minutes.

* Some paths have stairs. The robots *cannot* use stairs.

* Certain intersections are busy crosswalks. The robot must wait an additional $3$ minutes at these specific intersections before it can safely leave them and continue on its path.

**The Problem:**
Standard Dijkstra's algorithm only understands *edges* and *edge weights*. It does not natively understand "node weights" (like waiting at an intersection) or "impassable terrain".

**Questions:**

1. How should you represent the paths with stairs in your graph so the algorithm never tries to use them?

   > You can represent these edge weights as infinite, or nearly infinite, so that it will never be the shortest path for the robot to take, in terms of the total weight.

2. How can you account for the $3$-minute waiting time at busy crosswalk intersections using *only* edge weights? (Hint: Think about how you could modify the weights of edges connecting to that intersection).

   > You can make the edge weight equal to 5, since it takes 2 minutes ordinarily to go between sidewalk intersections, but the additional three minute wait means it takes a total of five minutes to cross an intersection with a crosswalk. Thus we make the edges for crosswalks have weight 5, and the edges for ordinary intersections have weight 2, to represent this difference.