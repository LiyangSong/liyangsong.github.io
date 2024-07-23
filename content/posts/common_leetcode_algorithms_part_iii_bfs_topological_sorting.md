---
title: "Common LeetCode Algorithms Part III: BFS & Topological Sorting"
date: 2024-07-22T20:52:47-04:00
description: "BFS is one of the most commonly used algorithms in tech interviews, yet perhaps the most straightforward one."
tags: [LeetCode, Algorithm, Python]
featured_image: "/img/math.jpg"
images: []
categories: LeetCode
comment: true
draft: false
---

## Common LeetCode Algorithms
- [I. Binary Search](../common_leetcode_algorithms_part_i_binary_search)
- [II. Binary Tree & Divide and Conquer](../common_leetcode_algorithms_part_ii_binary_tree_divide_and_conquer)
- III. BFS & Topological Sorting
- [IV. DFS](../common_leetcode_algorithms_part_iv_dfs)
- [V. Graph](../common_leetcode_algorithms_part_v_graph)
- [VI. Two Pointer & Linked List](../common_leetcode_algorithms_part_vi_two_pointer_linkededlist)
- [VII. Sliding Window & Sweep Line](../common_leetcode_algorithms_part_vii_sliding_window_sweep_line)
- [VIII. Heap & Top K & Mono Stack](../common_leetcode_algorithms_part_viii_heap_top_k_mono_stack)
- [IX. Prefix Sum & Stack & DP](../common_leetcode_algorithms_part_ix_prefix_sum_stack_dp)

&nbsp;

## Introduction

**Breadth First Search (BFS)** is a fundamental graph traversal algorithm that explores all nodes at the current level before moving to the next. In a tree data structure, BFS starts at the root and explores all nodes in a level-by-level manner. An additional **Queue** data structure is used to keep track of child nodes that are encountered but not yet explored.

- **Applications**: 
  - Level Order Traversal
  - Topological Sorting
  - Shortest Path in Simple Graphs (Equal Weights)

- **Key Questions**:
  - What is the starting state of BFS?
  - What data structure is used in the queue?
  - Should level information (size) be recorded? Does the initial level start from 0 or 1?
  - Should visited nodes be recorded?
    - Update visited before entering queue: The start node should be added to visited or result first.
    - Update visited after leaving the queue: Visited and queue should be checked for duplicates before nodes entering the queue.

- **If a problem can be solved by BFS, avoid using DFS!** BFS is simpler to write and less prone to errors.

&nbsp;

## Level Order Traversal Type

When traversing levels, increment the step after completing the current level traversal. (Step could start from 0 or -1, depending on the problem.)

Using `for _ in range(len(queue))` to record current level is optional. It can be used to process each level separately. If level information is not needed, the `while` loop will directly proceed to the next level after completing the current level.

If recording the result of the current level, note that `temp` could be empty in the last round. Check it before adding it to `res`.

The time complexity is _O(n)_, as each node in the tree is visited exactly one. The space complexity is _O(n)_ for the queue data structure.

**Pattern**:

```python
def bfs(self, node, res):
    queue = collections.deque()
    queue.append(node)
    while queue:
        temp = []
        for _ in range(len(queue)):
            curr = queue.popleft()
            if curr: 
                temp.append(curr.val) 
                queue.append(curr.left)
                queue.append(curr.right)
        if temp:
            res.append(temp)
```

&nbsp;

#### [102. Binary Tree Level Order Traversal](https://leetcode.com/problems/binary-tree-level-order-traversal/description/)

A standard BFS traversal problem. For each level, use `temp` to record the result of the current level and append it to the final result. Note that `temp` could be empty, so check it before appending.

```python
class Solution:
    def levelOrder(self, root: Optional[TreeNode]) -> List[List[int]]:
        rst = []
        queue = collections.deque()
        queue.append(root)
        while queue:
            temp = []
            for _ in range(len(queue)):
                curr = queue.popleft()
                if curr:
                    temp.append(curr.val)
                    queue.append(curr.left)
                    queue.append(curr.right)
            if temp:
                rst.append(temp)
        return rst              
```

&nbsp;

#### [103. Binary Tree Zigzag Level Order Traversal](https://leetcode.com/problems/binary-tree-zigzag-level-order-traversal/description/)

For even-numbered levels (0-indexed), add nodes normally (from left to right). For odd-numbered levels, add nodes from right to left. Record the level and insert nodes into `temp` accordingly.

```python
class Solution:
    def zigzagLevelOrder(self, root: Optional[TreeNode]) -> List[List[int]]:
        rst = []
        queue = collections.deque()
        queue.append(root)
        level = 0
        while queue:
            temp = []
            for _ in range(len(queue)):
                curr = queue.popleft()
                if curr:
                    if level % 2 == 0:
                        temp.append(curr.val)
                    else:
                        temp.insert(0, curr.val)
                    queue.append(curr.left)
                    queue.append(curr.right)
            level += 1
            if temp:
                rst.append(temp)
        return rst
```

&nbsp;

#### [199. Binary Tree Right Side View](https://leetcode.com/problems/binary-tree-right-side-view/description/)

For each level, instead of adding all nodes to `temp`, only add the rightmost (non-null) node.

```python
class Solution:
    def rightSideView(self, root: Optional[TreeNode]) -> List[int]:
        rst = []
        queue = collections.deque()
        queue.append(root)
        while queue:
            temp = []
            for _ in range(len(queue)):
                curr = queue.popleft()
                if curr:
                    temp.append(curr.val)
                    queue.append(curr.left)
                    queue.append(curr.right)
            if temp:
                rst.append(temp[-1])
        return rst        
```

&nbsp;

#### [314. Binary Tree Vertical Order Traversal](https://leetcode.com/problems/binary-tree-vertical-order-traversal/description/)

Traverse all nodes and record their row and column numbers. Start from the root node, with the column number as 0 and row as 0. The left child should have `row + 1` and `column - 1`, while the right child should have `row + 1` and `column + 1`.

Use a dictionary of lists to record the information, where the key is the column number, and elements in the list are tuples of `(row number, node value)`. After traversing all nodes, sort the dictionary by key (column) and then by tuple (row) to get the required result.

```python
class Solution:
    def verticalOrder(self, root: Optional[TreeNode]) -> List[List[int]]:
        rst = defaultdict(list)
        queue = collections.deque()
        queue.append((0, 0, root))
        
        while queue:
            curr_col, curr_row, curr_node = queue.popleft()
            if curr_node:
                rst[curr_col].append((curr_row, curr_node.val))
                queue.append((curr_col - 1, curr_row + 1, curr_node.left))
                queue.append((curr_col + 1, curr_row + 1, curr_node.right))
        
        result = []
        for col in sorted(rst):
            temp = []
            for row, val in sorted(rst[col], key=lambda x: x[0]):
                temp.append(val)
            result.append(temp)
        return result
```

DFS method can also be applied to traverse the tree:

```python
class Solution:
    def verticalOrder(self, root: Optional[TreeNode]) -> List[List[int]]:
        self.rst = defaultdict(list)
        self.dfs(0, 0, root)
        
        result = []
        for col in sorted(self.rst):
            temp = []
            for row, val in sorted(self.rst[col], key=lambda x: x[0]):
                temp.append(val)
            result.append(temp)
        return result
    
    def dfs(self, col, row, root):
        if not root:
            return
        
        self.rst[col].append((row, root.val))
        
        if root.left:
            self.dfs(col - 1, row + 1, root.left)
        if root.right:
            self.dfs(col + 1, row + 1, root.right)
```

&nbsp;

#### [297. Serialize and Deserialize Binary Tree](https://leetcode.com/problems/serialize-and-deserialize-binary-tree/description/)

For serialization, traverse the tree using BFS and record all node values. To ensure the tree structure is maintained during deserialization, include `None` values in the result. Use 'N' to represent `None`. Note that the last level might contain all `None`s, which should be excluded from the result.

For deserialization, convert the serialized string back into a list and rebuild the tree using BFS. Traverse the list, adding values to the tree (create a new child node) in a `root-left-right` order (preorder). Skip to the next value when encountering 'N'. After traversal, return the root node of the tree.

```python
class Codec:

    def serialize(self, root: TreeNode) -> str:
        """Encodes a tree to a single string.
        
        :type root: TreeNode
        :rtype: str
        """
        
        rst = []
        queue = collections.deque()
        queue.append(root)
        while queue:
            temp = []
            for _ in range(len(queue)):
                curr = queue.popleft()
                if curr:
                    temp.append(curr.val)
                    queue.append(curr.left)
                    queue.append(curr.right)
                else:
                    temp.append(None)
            
            if any(i is not None for i in temp):
                for i in temp:
                    if i is not None:  
                        rst.append(str(i))
                    else:
                        rst.append('N')

        return ','.join(rst)


    def deserialize(self, data: str) -> TreeNode:
        """Decodes your encoded data to tree.
        
        :type data: str
        :rtype: TreeNode
        """

        if data == "":
            return None

        data = data.split(',')
        root = TreeNode(int(data[0]))
        queue = collections.deque()
        queue.append(root)
        i = 1

        while queue and i < len(data):
            curr = queue.popleft()

            if data[i] != 'N':
                curr.left = TreeNode(int(data[i]))
                queue.append(curr.left)
            i += 1

            if data[i] != 'N':
                curr.right = TreeNode(int(data[i]))
                queue.append(curr.right)
            i += 1
        
        return root


# Your Codec object will be instantiated and called as such:
# ser = Codec()
# deser = Codec()
# ans = deser.deserialize(ser.serialize(root))
```

&nbsp;

#### [515. Find Largest Value in Each Tree Row](https://leetcode.com/problems/find-largest-value-in-each-tree-row/description/)

Record the maximum value of `temp` for each level during BFS traversal.

```python
class Solution:
    def largestValues(self, root: Optional[TreeNode]) -> List[int]:
        rst = []
        queue = collections.deque()
        queue.append(root)
        while queue:
            temp = []
            for _ in range(len(queue)):
                curr = queue.popleft()
                if curr:
                    temp.append(curr.val)
                    queue.append(curr.left)
                    queue.append(curr.right)
            if temp:
                rst.append(max(temp))
        return rst
```

&nbsp;

#### [1448. Count Good Nodes in Binary Tree](https://leetcode.com/problems/count-good-nodes-in-binary-tree/description/)

Record the previous maximum value in the path from the root to the current node. If the current value is greater than or equal to that, the node is a good node. Record both the node and the previous maximum in the queue and use a variable to count the number of good nodes.

For a good node (`curr.value >= prev_max`), its value becomes the `prev_max` of its children.

```python
class Solution:
    def goodNodes(self, root: TreeNode) -> int:
        rst = 0
        queue = collections.deque()
        queue.append((root, float('-inf')))  # record previous max in the path
        
        while queue:
            curr, prev_max = queue.popleft()
            if curr:
                curr_max = prev_max
                if curr.val >= prev_max:
                    rst += 1
                    curr_max = curr.val
                queue.append((curr.left, curr_max))
                queue.append((curr.right, curr_max))

        return rst
```

&nbsp;

#### [637. Average of Levels in Binary Tree](https://leetcode.com/problems/average-of-levels-in-binary-tree/description/)

Record the average value of `temp` for each level during BFS traversal.

```python
class Solution:
    def averageOfLevels(self, root: Optional[TreeNode]) -> List[float]:
        rst = []
        queue = collections.deque()
        queue.append(root)

        while queue:
            temp = []
            for _ in range(len(queue)):
                curr = queue.popleft()
                if curr:
                    temp.append(curr.val)
                    queue.append(curr.left)
                    queue.append(curr.right)
            if temp:
                rst.append(sum(temp) / len(temp))
        
        return rst
```

&nbsp;

#### [958. Check Completeness of a Binary Tree](https://leetcode.com/problems/check-completeness-of-a-binary-tree/description/)

A complete tree should not have `None` nodes before non-`None` nodes. Use a boolean variable to mark that a `None` has been encountered during BFS traversal. If a `None` is encountered from the queue, mark the variable as `True`. If a non-`None` node is encountered after that, directly return `False`.

```python
class Solution:
    def isCompleteTree(self, root: Optional[TreeNode]) -> bool:
        queue = collections.deque()
        queue.append(root)
        hasNone = False  # Mark having seen a None
        
        while queue:
            curr = queue.popleft()
            if curr:
                if hasNone:
                    return False
                queue.append(curr.left)
                queue.append(curr.right)
            else:
                hasNone = True

        return True
```

&nbsp;

#### [1161. Maximum Level Sum of a Binary Tree](https://leetcode.com/problems/maximum-level-sum-of-a-binary-tree/description/)

Record the sum of `temp` for each level and keep a global variable `max_sum` to track the maximum sum at each level. Also, update the result to the level where `max_sum` is found.

```python
class Solution:
    def maxLevelSum(self, root: Optional[TreeNode]) -> int:
        rst = 0
        max_sum = float('-inf')
        level = 0

        queue = collections.deque()
        queue.append(root)

        while queue:
            level += 1
            temp = []
            for _ in range(len(queue)):
                curr = queue.popleft()
                if curr:
                    temp.append(curr.val)
                    queue.append(curr.left)
                    queue.append(curr.right)
            
            if temp:
                if sum(temp) > max_sum:
                    max_sum = sum(temp)
                    rst = level
        
        return rst
```

&nbsp;

#### [559. Maximum Depth of N-ary Tree](https://leetcode.com/problems/maximum-depth-of-n-ary-tree/description/)

To start a BFS, add the root node to the queue. Track the level counts of the BFS to determine the depth. When the BFS completes, the recorded level counts will indicate the maximum depth.

For each node, explore its children and add any non-null children to the queue.

```python
class Solution:
    def maxDepth(self, root: 'Node') -> int:
        if not root:
            return 0

        queue = collections.deque()
        queue.append(root)
        depth = 0

        while queue:
            depth += 1
            for _ in range(len(queue)):
                curr = queue.popleft()
                for child in curr.children:
                    if child:
                        queue.append(child)
            
        return depth
```

&nbsp;

## Shortest Path in Simple Graph

BFS can also be used to find the shortest path in a graph. For this type of question, instead of adding children to the queue, we add neighbors of the current node to the queue and increase step when completing (or before, according to the problem) the traversal of one level. The final step after BFS traversal will be the shortest path.

One important issue in the shortest path problem is to identify which nodes should be added to the queue before starting BFS, i.e., where should we start the traversal in the graph. Additionally, to avoid adding nodes redundantly, we should record visited nodes and always check that the neighbor does not exceed the boundary of the graph (e.g., row and column boundaries of a grid).

The visited nodes can be recorded in two ways:
- Record visited nodes in a set and only append neighbors that are not in the set.
- Modify all visited nodes to a specific value and only append neighbors that have not been changed.

For the shortest path in graph questions, the time complexity is _O(V + E)_ as we visit all nodes and edges (when checking all possible neighbors). The space complexity is _O(V)_ for the queue and visited set data structures.

**Pattern**:

```python
def bfs(self, grid, queue, visited):
    step = -1
    while queue:
        size = len(queue)
        for _ in range(size):
            x,y = queue.popleft()
            for dx,dy in [(0,1),(0,-1),(1,0),(-1,0)]:
                newx = x + dx
                newy = y + dy
                if self.isValid(newx,newy, grid, visited):
                    visited.add((newx,newy))
                    queue.append((newx,newy))
        step +=1 
    return step
    
def isValid(self, x, y, grid, visited):
    return 0 <= x< len(grid) and 0 <= y < len(grid[0]) and (x,y) not in visited and grid[x][y] == 1
```

&nbsp;

#### [200. Number of Islands](https://leetcode.com/problems/number-of-islands/description/)

For this problem, the BFS traversal should start from any "1" node (we can traverse the grid to find any). During BFS, every "1" encountered is changed to "0" before entering the queue (taking the function of `visited`).

For neighbors of one node, we check its value and skip those equal to "0" (already in `visited`). We also check its row and column numbers to avoid exceeding the boundary of the grid. 

After completing one BFS, all nodes in the current "island" have been visited (changed to "0"). Then we continue to traverse the grid to find next "1" node (next island). `rst` is increased by 1 for each new BFS, representing the number of islands.

```python
class Solution:
    def numIslands(self, grid: List[List[str]]) -> int:
        rst = 0
        queue = collections.deque()

        for x in range(len(grid)):
            for y in range(len(grid[0])):
                if grid[x][y] == '1':   
                    rst += 1 
                    queue.append((x, y))
                    grid[x][y] = '0'
        
                    while queue:
                        curr_x, curr_y = queue.popleft()
                        for dx, dy in [(0, -1), (-1, 0), (0, 1), (1, 0)]:
                            new_x = curr_x + dx
                            new_y = curr_y + dy
                            if 0 <= new_x < len(grid) and 0 <= new_y < len(grid[0]) and grid[new_x][new_y] == '1':
                                queue.append((new_x, new_y))
                                grid[new_x][new_y] = '0' 

        return rst
```

&nbsp;

#### [994. Rotting Oranges](https://leetcode.com/problems/rotting-oranges/description/)

Similar to [200. Number of Islands](https://leetcode.com/problems/number-of-islands/description/), but the rotting process starts from all rotted oranges. This means we should traverse the grid to put all rotten oranges in the queue and mark them as visited. We should also record total number of fresh oranges as we need to know when we have visited (rotted) all fresh oranges and need to stop.

Each time we visit a neighbor, we change its value to 2 (rotted) and deduce the number of fresh oranges. After the BFS, if the number of fresh oranges equals 0, return the number of steps of BFS. Otherwise, it means some orange cannot be achieved, and we should return -1.

```python
class Solution:
    def orangesRotting(self, grid: List[List[int]]) -> int:
        fresh_count = 0
        queue = collections.deque()
        for x in range(len(grid)):
            for y in range(len(grid[0])):
                if grid[x][y] == 1:
                    fresh_count += 1
                if grid[x][y] == 2:
                    queue.append((x, y))
        
        if fresh_count == 0:
            return 0

        minute = -1
        while queue:
            minute += 1
            for _ in range(len(queue)):
                curr_x, curr_y = queue.popleft()
                for dx, dy in [(-1, 0), (0, 1), (1, 0), (0, -1)]:
                    new_x = curr_x + dx
                    new_y = curr_y + dy
                    if 0 <= new_x < len(grid) and 0 <= new_y < len(grid[0]) and grid[new_x][new_y] == 1:
                        queue.append((new_x, new_y))
                        grid[new_x][new_y] = 2
                        fresh_count -= 1
 
        if fresh_count == 0:
            return minute
        return -1
```

&nbsp;

#### [286. Walls and Gates](https://leetcode.com/problems/walls-and-gates/description/)

This problem asks for the number of steps from empty rooms to the nearest gates. If we start from empty rooms and perform BFS to find the nearest gate, we need to execute this process for every empty room, which could be inefficient. Instead, as the number of gates is much smaller than the number of empty rooms, we can start from gates and use BFS to find the shortest path to all empty rooms.

Traverse the grid and add gates (value equals 0) to the queue to start a BFS traversal. When an empty room is first reached, update its value to the distance (steps of BFS) from the gate. For neighbors of each node, we should check whether they are already visited (has a distance) and are valid (row and column numbers not exceeding the boundary).

```python
class Solution:
    def wallsAndGates(self, rooms: List[List[int]]) -> None:
        """
        Do not return anything, modify rooms in-place instead.
        """
        
        queue = collections.deque()
        for x in range(len(rooms)):
            for y in range(len(rooms[0])):
                if rooms[x][y] == 0:
                    queue.append((x, y))
        
        distance = 0
        while queue:
            distance += 1
            for _ in range(len(queue)):
                curr_x, curr_y = queue.popleft()
                for dx, dy in [(0, -1), (1, 0), (0, 1), (-1, 0)]:
                    new_x = curr_x + dx
                    new_y = curr_y + dy
                    if 0 <= new_x < len(rooms) and 0 <= new_y < len(rooms[0]) and rooms[new_x][new_y] == 2147483647:
                        queue.append((new_x, new_y))
                        rooms[new_x][new_y] = distance
```

&nbsp;

#### [490. The Maze](https://leetcode.com/problems/the-maze/description/)

In this problem, within the BFS traversal, neighbors are not nodes nearby in 4 directions. Instead, as the ball will go until reaching a wall, neighbors of one node are nodes that are at the end of 4-direction paths. For each direction, we should check each next node, and if the ball can pass by (not a wall), it continues to move until hitting a wall. This can be achieved with a `while` loop that moves step by step until reaching the wall (boundary of the maze).

Record stop points in `visited` and add them to the queue. When we reach a stop point that equals the `destination`, return True.

Note in this question, directly changing visited nodes to a specific value (e.g. 1) is not advisable because we also reply on node values to check whether we have not hit a wall and can continue to pass.

```python
class Solution:
    def hasPath(self, maze: List[List[int]], start: List[int], destination: List[int]) -> bool:
        visited = set()
        queue = collections.deque()
        queue.append(tuple(start))
        visited.add(tuple(start))

        while queue:
            curr_x, curr_y = queue.popleft()
            for dx, dy in [(-1, 0), (0, 1), (1, 0), (0, -1)]:
                new_x, new_y = curr_x, curr_y
                if [new_x, new_y] == destination:
                    return True
                
                while 0 <= new_x + dx < len(maze) and 0 <= new_y + dy < len(maze[0]) and maze[new_x + dx][new_y + dy] == 0:
                    new_x += dx
                    new_y += dy
                
                if (new_x, new_y) not in visited:
                    queue.append((new_x, new_y))
                    visited.add((new_x, new_y))
        
        return False
```

&nbsp;

#### [909. Snakes and Ladders](https://leetcode.com/problems/snakes-and-ladders/description/)

The neighbors of current node are the next 6 nodes, so it is better to transform the 2-D board into a 1-D list. Note to check the row number to determine whether to add the row from left to right or reverse.

Perform a BFS process starting form the first element of the list. Add steps by 1 for each round of BFS. For each node, check its neighbors (next 6 nodes). If the value of neighbors equals -1, add their indices to the queue. Otherwise, add the indices of the corresponding values the queue.

Check whether the current node index equals `n * n - 1` to determine if the end point is reached and return the step number. If BFS completes without returning, return -1.

```python
class Solution:
    def snakesAndLadders(self, board: List[List[int]]) -> int:
        n = len(board)
        board_list = []
        for row in range(n - 1, -1, -1):
            if (row - n - 1) % 2 == 0:
                board_list += board[row]
            else:
                board_list += board[row][::-1]
            
        queue = collections.deque()
        visited = set()
        step = 0
        
        queue.append(0)
        visited.add(0)
        
        while queue:
            for _ in range(len(queue)):
                curr = queue.popleft()
                if curr == n * n - 1:
                    return step

                for move in range(1, 7):
                    next = curr + move
                    if next < n * n:
                        if board_list[next] != -1:
                            next = board_list[next] - 1
                        if next not in visited:
                            queue.append(next)
                            visited.add(next)
            step += 1
        
        return -1
```

&nbsp;

#### [684. Redundant Connection](https://leetcode.com/problems/redundant-connection/description/)

Before building the adjacent table from the `edges`, which means adding the current edge to the graph, check whether there is already a path from one node to another in the edge. If yes, the edge would be redundant and should be the result. If no, we adding it to the adjacent table.

```python
class Solution:
    def findRedundantConnection(self, edges: List[List[int]]) -> List[int]:
        def bfs(u, v):
            queue = collections.deque()
            visited = set()
            queue.append(u) 
            visited.add(u)
        
            while queue:
                curr = queue.popleft()
                if curr == v:
                    return True
                for neighbor in adj_table[curr]:
                    if neighbor not in visited:
                        queue.append(neighbor)
                        visited.add(neighbor)
            return False
        
        adj_table = defaultdict(set)
        for u, v in edges:
            if bfs(u, v):
                return [u, v]
            adj_table[u].add(v)
            adj_table[v].add(u)
```

&nbsp;

#### [317. Shortest Distance from all Buildings](https://leetcode.com/problems/shortest-distance-from-all-buildings/description/)

This problem requires finding the shortest distance from all buildings to one empty cell. To solve this, we can perform a BFS process for each building, updating the distances for all empty cells, and ultimately finding the one with the smallest sum of distances.

Traverse the grid to find all buildings (`1`s) and perform BFS from each building. During BFS, for four neighbors of the current node, check if they are empty cells, have not been visited, and are within the boundary before adding them to the queue. Count the number of buildings and empty cells during the grid traversal.

Maintain a list of distances for each empty cell, representing the distances to all buildings. The level of BFS would be the distance from the current node to the current building that performs the BFS.

After all BFS processes, ignore cells whose `distances` lists have fewer elements than the number of buildings (indicating that some buildings cannot be reached from those cell). if the number of empty cells is 0 after traversal, return -1.

Finally, calculate and find the cell with the minimum sum of distances.

```python
class Solution:
    def shortestDistance(self, grid: List[List[int]]) -> int:
        queue = collections.deque()
        distances = defaultdict(list)
        land_num = 0
        house_num = 0

        for x in range(len(grid)):
            for y in range(len(grid[0])):
                if grid[x][y] == 0:
                    land_num += 1

                if grid[x][y] == 1:
                    house_num += 1
                    queue.append((x, y))
                    visited = set()
                    distance = 0
                    
                    while queue:
                        distance += 1
                        for _ in range(len(queue)):
                            curr_x, curr_y = queue.popleft()
                            for dx, dy in [(-1, 0), (0, 1), (1, 0), (0, -1)]:
                                new_x, new_y = curr_x + dx, curr_y + dy
                                if 0 <= new_x < len(grid) and 0 <= new_y < len(grid[0]) and grid[new_x][new_y] == 0 and (new_x, new_y) not in visited:
                                    distances[(new_x, new_y)].append(distance)
                                    queue.append((new_x, new_y))
                                    visited.add((new_x, new_y))
        
        if land_num == 0:
            return -1

        min_distance_sum = float('inf')
        for distance in distances.values():
            if len(distance) != house_num:
                continue
            min_distance_sum = min(min_distance_sum, sum(distance))
        
        return min_distance_sum if min_distance_sum != float('inf') else -1
```

&nbsp;

#### [752. Open the Lock](https://leetcode.com/problems/open-the-lock/description/)

To solve the problem of finding the minimum steps to the target from `"0000"`, we can perform a BFS process. If `"0000"` is right in the `deadends`, return -1 immediately.

The key to this problem is identifying neighbors of a given node. For 4 circular wheels, they could be 8 possible moves as each wheel can increment by 1 or decrement by 1. For each move, we must ensure the wheel wraps around by changing `-1` to `9` and changing `10` to `0`. We should skip neighbors that are in the `deadends` or already visited.

The level of BFS represents the number of steps. When the current node matches the target, we should return the number of steps as the result. If the BFS completes without returning, return -1.

```python
class Solution:
    def openLock(self, deadends: List[str], target: str) -> int:
        queue = collections.deque()
        visited = set()
        
        if "0000" in deadends:
            return -1
        
        queue.append("0000")
        visited.add("0000")
        steps = 0
        
        while queue:
            for _ in range(len(queue)):
                curr = queue.popleft()
                if curr == target:
                    return steps
                for i, move in [(0, -1), (0, 1), (1, -1), (1, 1), (2, -1), (2, 1), (3, -1), (3, 1)]:                   
                    new_num = int(curr[i]) + move
                    if new_num == -1:
                        new_num = 9
                    if new_num == 10:
                        new_num = 0
                    next = curr[:i] + str(new_num) + curr[i+1:]
                    if next not in visited and next not in deadends:
                        queue.append(next)
                        visited.add(next)
            steps += 1
        return -1
```

&nbsp;

## Topological Sorting

**Topological Sorting** is used to sort nodes in a directed graph such that for every edge in the graph, the start vertex of the edge always occurs earlier in the sequence than the end vertex of the edge. A topological sorting is possible if and only if the graph has no directed cycles, that is, if it is a **directed acyclic graph (DAG)**.

In topological sorting, we use an adjacent table (hashmap) `int: List(int)` to represent the graph, where the key is the ingoing node and the value is a list of its outgoing nodes. During this process, we also update the in-degree of all nodes in an `in_degree` hashmap. 

After building the adjacent table, nodes that have an in-degree of 0 are added to the queue to start the BFS. Each time a node is removed from the queue, its neighbors' in-degrees are updated, and neighbors with an in-degree of 0 are added to the queue (rather than directly adding neighbors to the queue). We record each added node in the result, which finally becomes the sorted sequence.

For topological sorting, the time complexity is _O(V + E)_ as we visit all nodes and edges (when checking all neighbors). The space complexity is _O(V)_ for the queue and visited set data structures.

&nbsp;

#### [207. Course Schedule](https://leetcode.com/problems/course-schedule/description/)

The first step is to build the adjacent table and in-degree table based on the given prerequisites. Initialize the in-degree table with all course having an in-degree of 0, then go through the `prerequisites` and update the adjacent table and in-degree.

Next, traverse the in-degree table to add all courses with an in-degree of 0 to the queue. Perform a BFS, removing nodes from the queue, checking theirs neighbors in the adjacent table, updating neighbors' in-degrees, and adding all new 0-in-degree courses to the queue.

As not all coursers may be completed at last (all added to the queue), we should record visited courses and check `len(visited) == numCourses` at the end.

```python
class Solution:
    def canFinish(self, numCourses: int, prerequisites: List[List[int]]) -> bool:
        in_degree = defaultdict(int)
        for course in range(numCourses):
            in_degree[course] = 0

        adj_table = defaultdict(set)
        for course_pair in prerequisites:
            adj_table[course_pair[1]].add(course_pair[0])
            in_degree[course_pair[0]] += 1

        visited = set()
        queue = collections.deque()
        for course in in_degree:
            if in_degree[course] == 0:
                queue.append(course)
                visited.add(course)
        
        while queue:
            curr = queue.popleft()
            for course in adj_table[curr]:
                in_degree[course] -= 1
                if in_degree[course] == 0:
                    queue.append(course)
                    visited.add(course)
        
        return len(visited) == numCourses        
```

&nbsp;

#### [210. Course Schedule II](https://leetcode.com/problems/course-schedule-ii/description/)

Similar to [207. Course Schedule](https://leetcode.com/problems/course-schedule/description/), but instead of determining whether all courses can be completed, it asks for the path we visit courses. Note to add an extra check `len(rst) != numCourses` to return `[]` if not all courses can be visited.

```python
class Solution:
    def findOrder(self, numCourses: int, prerequisites: List[List[int]]) -> List[int]:
        indegree = defaultdict(int)
        for course in range(numCourses):
            indegree[course] = 0
        
        graph = defaultdict(list)
        for course_pair in prerequisites:
            graph[course_pair[1]].append(course_pair[0])
            indegree[course_pair[0]] += 1
        
        queue = collections.deque()
        rst = []
        for course in indegree:
            if indegree[course] == 0:
                queue.append(course)
                rst.append(course)

        while queue:
            curr = queue.popleft()
            for course in graph[curr]:
                indegree[course] -= 1
                if indegree[course] == 0:
                    queue.append(course)
                    rst.append(course)
        
        if len(rst) != numCourses:
            return []
        return rst
```

&nbsp;

## Dijkstra (Shortest Path in Weighted Graph)

For shortest path in weighted graph problems, Dijkstra's Algorithm is commonly used. The difference between Dijkstra and BFS is that Dijkstra maintains a **Minimum Heap** rather than a queue. Heap is not ordered but can make sure the top number that popped out is always minimum/maximum. In python, the default heap is min-heap (`heapq.heappop` and `heapq.heappush` to a list).

At the beginning, add the start point (a tuple of `(cost, node)`) to the heap. Each time, the heap pops out the node with the lowest cost. Then check its neighbors in the graph's adjacent table. Check whether they are visited and add current node's cost to the cost value before adding neighbors to the heap.

For Dijkstra, the time complexity is _O(V + E) logV_. The running time of popping out the minimum from the heap is _O(VlogV)_. After popping, we explore neighbors (_O(E)_) and then update the heap (_O(logV)_) each time.

The space complexity is _O(V)_ for the heap and visited set data structures.

**Pattern**:

```python
def dijkstra(self, heap, n, graph):
    visited = {}
    
    while heap:
        curr, node = heapq.heappop(heap)
        if node in visited and visited[node] < curr:
            continue
        visited[node] = curr
        if len(visited) == n:
            return curr
        for val, nextNode in graph[node]:
            if nextNode not in visited:
                heapq.heappush(heap, (val + curr, nextNode))
    return -1
```

&nbsp;

#### [743. Network Delay Time](https://leetcode.com/problems/network-delay-time/description/)

The first step is to build the adjacent table based on `times`. The adjacent table stores a tuple of `(time, node)`.

Add the start node `(0, k)` to the heap and perform a BFS-like process. Pop the minimum from the heap, check its out neighbors in the adjacent table, and add neighbors to the heap with updated time costs.

When popping from the heap, check whether all nodes have been visited and directly return current time cost if all nodes are visited. Dijkstra always follows the lowest-cost path and this will be the result.

```python
class Solution:
    def networkDelayTime(self, times: List[List[int]], n: int, k: int) -> int:
        adj_table = defaultdict(list)
        for time in times:
            adj_table[time[0]].append((time[2], time[1]))
        
        heap = []
        visited = set()
        heapq.heappush(heap, (0, k))
       
        while heap:
            curr_time, curr = heapq.heappop(heap)
            visited.add(curr)
            if len(visited) == n:
                return curr_time
            
            for time, neighbor in adj_table[curr]:
                if neighbor not in visited:
                    heapq.heappush(heap, (curr_time + time, neighbor))
    
        return -1
```

&nbsp;

#### [787. Cheapest Flights Within K Stops](https://leetcode.com/problems/cheapest-flights-within-k-stops/description/)

The first step is to build the adjacent table based on `flights`. The adjacent table stores a tuple of `(price, city)`. We should record not only the cumulative flight price but also the cumulative stops. So the heap should also record stop numbers `(price, city, stop)`. 

Add the start node `(0, src, 0)` to the heap and perform a BFS-like process. Pop the minimum price from the heap, check its out neighbors in the adjacent table, and add neighbors to the heap with updated prices and updated stop numbers.

When popping from the heap, ignore if `curr_stop > k + 1`. We will not use a visited set, as the path with the lowest price may have a stop number larger than `k + 1`, and we need to backtrack and check other paths. Instead, use a `stops` dict to record the visited smallest stop number in each node, skipping the current node if it has a larger recorded stop number in the `stops` dict. This means the previous path passing this node has a smaller stop number and is still invalid, so no need to consider the current path anymore.

For the end point, check whether current node is the destination city. If so, directly return the current price as the final result.

```python
class Solution:
    def findCheapestPrice(self, n: int, flights: List[List[int]], src: int, dst: int, k: int) -> int:
        adj_table = defaultdict(list)
        for flight in flights:
            adj_table[flight[0]].append((flight[2], flight[1]))  # price, city
        
        heap = []
        stops = defaultdict(lambda: float('inf'))
        heapq.heappush(heap, (0, src, 0))  # price, city, stop

        while heap:
            curr_price, curr_city, curr_stop = heapq.heappop(heap)
            if curr_stop > k + 1 or curr_stop > stops[curr_city]:
                continue

            if curr_city == dst:
                return curr_price 
            
            stops[curr_city] = curr_stop
            
            for price, city in adj_table[curr_city]:
                heapq.heappush(heap, (curr_price + price, city, curr_stop + 1))
        
        return -1  
```

&nbsp;

## Summary

BFS is one of the most commonly used algorithms in tech interviews. It can be applied to traverse a tree or graph to find a target value, count a metric, find the shortest path, or perform topological sorting. On the other hand, it is perhaps the most straightforward problem to solve, as its solution typically follows a fixed pattern:
1. Initialize a queue and add the starting point (the root of a tree, one or several specific nodes of a graph, etc.).
2. Pop a node from the queue, find its neighbors, and add valid neighbors to the queue.
3. Repeat until the queue is empty.

The key is to understand how the answer can be built across levels of the queue (e.g. maintain a global variable), how to check if neighbors are valid (not visited, not exceeding boundaries, etc.), and when to stop (e.g. upon finding the target or the queue becoming empty). 

Using `for _ in range(len(queue))` is a useful trick to separate each level of BFS, and can be helpful when processing level-specific information.

Dijkstra’s algorithm is essentially a modified form of BFS that uses a `heap` (priority queue) rather than a standard `queue`. Note python uses min-heap by default with `heapq.heappop` and `heapq.heappush` operations.

