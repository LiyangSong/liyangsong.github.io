---
title: "Common LeetCode Algorithms Part II: Binary Tree & Divide and Conquer"
date: 2024-06-15T18:32:07-04:00
description: "Divide and Conquer can solve most tree problems using DFS to process return values of children for each root node."
tags: [LeetCode, Algorithm, Python]
featured_image: "/img/math.jpg"
images: []
categories: LeetCode
comment: true
draft: false
---

## Introduction

Both Traversal and Divide and Conquer are iteration methods that can be written using either recursion or iteration, though recursion is more commonly used.
- **Traversal** involves visiting each node in the tree, typically using a Depth-First Search (DFS) method. It often utilizes a global variable passed down during the traversal.
- **Divide and Conquer** usually does not use a global variable. It processes the return values of the left and right children to determine the current node's return value, focusing on building a return value from the children's values.

Most tree problems can be effectively solved using the **Divide and Conquer** approach.

Three traversal notions:
- **Preorder**: root, left, right
- **Postorder**: left, right, root
- **Inorder**: left, root, right
Note: Left and right can be either nodes or subtrees.

&nbsp;

## Binary Tree Search Pattern (Divide and Conquer)

```python
def dfs(self, root):
    if not root:
        return 0
  
    # Optional Leaf processing 
    if root.left is None and root.right is None: 
        return 1
        
    leftReturn = self.dfs(root.left)
    rightReturn = self.dfs(root.right)
        
    height = max(leftReturn, rightReturn) + 1
    return height
```

The leaf node processing is optional. Sometimes the leftReturn and rightReturn of a leaf node's children (both None) can be processed to get the desired value. However, sometimes the return of None children is just None, and we need to specify what should be returned from two None returns.

&nbsp;

## Tree Traversal Type

&nbsp;

#### [144. Binary Tree Preorder Traversal](https://leetcode.com/problems/binary-tree-preorder-traversal/description/)

In this traversal, we pass a result variable as an argument during recursion, updating it with the current root's value, then with the left subtree, and finally with the right subtree.

The time complexity is _O(n)_, as each node in the tree is visited exactly one. The space complexity is _O(logn)_, corresponding to the depth of the function call stack (the height of the tree).

In the following questions using Divide and Conquer to traverse the tree, unless specified otherwise, the time complexity and space complexity remain the same as tree traversal (_O(n)_ and _O(logn)_).

```python
class Solution:
    def preorderTraversal(self, root: Optional[TreeNode]) -> List[int]:
        rst = []
        self.dfs(root, rst)
        return rst
    
    def dfs(self, root: Optional[TreeNode], rst: List[int]) -> None:
        if not root:
            return
        rst.append(root.val)
        self.dfs(root.left, rst)
        self.dfs(root.right, rst)   
```

Alternatively, we can keep the result as a global variable and update it during the recursion.

```python
class Solution:
    def preorderTraversal(self, root: Optional[TreeNode]) -> List[int]:
        rst = []
        def dfs(root: Optional[TreeNode]) -> None:
            if not root:
                return
            rst.append(root.val)
            dfs(root.left)
            dfs(root.right)   
        dfs(root)
        return rst
```

&nbsp;

#### [94. Binary Tree Inorder Traversal](https://leetcode.com/problems/binary-tree-inorder-traversal/description/)

Similar to [144. Binary Tree Preorder Traversal](https://leetcode.com/problems/binary-tree-preorder-traversal/description/), but we update the result with the left subtree first, then the current root, and finally the right subtree.

```python
class Solution:
    def inorderTraversal(self, root: Optional[TreeNode]) -> List[int]:
        rst = []
        def dfs(root: Optional[TreeNode]) -> None:
            if not root:
                return
            dfs(root.left)
            rst.append(root.val)
            dfs(root.right)   
        dfs(root)
        return rst
```

&nbsp;

#### [145. Binary Tree Postorder Traversal](https://leetcode.com/problems/binary-tree-postorder-traversal/description/)

Similar to [144. Binary Tree Preorder Traversal](https://leetcode.com/problems/binary-tree-preorder-traversal/description/), but we update the result with the left subtree first, then the right subtree, and finally the current root.

```python
class Solution:
    def postorderTraversal(self, root: Optional[TreeNode]) -> List[int]:
        rst = []
        def dfs(root: Optional[TreeNode]) -> None:
            if not root:
                return
            dfs(root.left)
            dfs(root.right)
            rst.append(root.val)
        dfs(root)
        return rst
```

&nbsp;

#### [104. Maximum Depth of Binary Tree](https://leetcode.com/problems/maximum-depth-of-binary-tree/description/)

The children should return their depths, and the root should compare the depths and add 1 to determine its depth. The maximum depth of the tree is the depth of the root node.

```python
class Solution:
    def maxDepth(self, root: Optional[TreeNode]) -> int:
        return self.dfs(root)
        
    def dfs(self, root: Optional[TreeNode]) -> int:
        if not root:
            return 0
        leftReturn = self.dfs(root.left)
        rightReturn = self.dfs(root.right)
        return max(leftReturn, rightReturn) + 1
```

&nbsp;

#### [1120. Maximum Average Subtree](https://leetcode.com/problems/maximum-average-subtree/description/)

To calculate the new average of the current subtree, we need to know the sum and count. We should also compare the current max with its children's max. Thus, the children should return both the sum, count, and max_average of nodes in the subtree.

The root node should calculate the new average and new max_average from returns. Additionally, we can maintain a max_average global variable, and the children should return both sum and count.

```python
class Solution:
    def maximumAverageSubtree(self, root: Optional[TreeNode]) -> float:
        return self.dfs(root)[2]
    
    def dfs(self, root: Optional[TreeNode]) -> (int, int, int):
        if not root:
            return (0, 0, float('-inf'))
        
        left_sum, left_count, left_max_avg = self.dfs(root.left)
        right_sum, right_count, right_max_avg = self.dfs(root.right)
        
        curr_sum = root.val + left_sum + right_sum
        curr_count = 1 + left_count + right_count
        curr_max_avg = max(curr_sum / curr_count, left_max_avg, right_max_avg)

        return curr_sum, curr_count, curr_max_avg
```

&nbsp;

#### [110. Balanced Binary Tree](https://leetcode.com/problems/balanced-binary-tree/description/)

A balanced tree means any node in the subtrees should have a height difference no larger than 1. We can maintain a global variable to indicate whether the tree is balanced. The children should return their root's height for comparison.

The root should check if the height difference between the two subtrees is greater than 1, and if so, set the global variable to False. The root should return its own height (the maximum of the subtrees' heights plus 1).

```python
class Solution:
    def isBalanced(self, root: Optional[TreeNode]) -> bool:
        self.rst = True
        self.dfs(root)
        return self.rst
    
    def dfs(self, root: Optional[TreeNode]) -> int:
        if not root:
            return 0
        
        leftReturn = self.dfs(root.left)
        rightReturn = self.dfs(root.right)
        
        if abs(leftReturn - rightReturn) > 1:
            self.rst = False
            return 0
        
        return max(leftReturn, rightReturn) + 1
```

&nbsp;

#### [236. Lowest Common Ancestor of a Binary Tree](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-tree/description/)

If both P and Q are found from the children, then the root is the ancestor. We can use a global variable to record the found ancestor. Children should return whether they contain P or Q.

The root should check whether both children are true, or whether both the root and one child are true (i.e., whether two nodes out of three (root, children) are true (P or Q)).

```python
class Solution:
    def lowestCommonAncestor(self, root: 'TreeNode', p: 'TreeNode', q: 'TreeNode') -> 'TreeNode':
        self.rst = None
        self.dfs(root, p, q)
        return self.rst
    
    def dfs(self, root: 'TreeNode', p: 'TreeNode', q: 'TreeNode') -> bool:
        if not root:
            return False
        
        leftReturn = self.dfs(root.left, p, q)
        rightReturn = self.dfs(root.right, p, q)

        if leftReturn + rightReturn + (root in (p, q)) >= 1:
            if leftReturn + rightReturn + (root in (p, q)) == 2:
                self.rst = root
            return True

        return False
```

Another solution is to return the node that is P or Q directly.

```python
class Solution:
    def lowestCommonAncestor(self, root: 'TreeNode', p: 'TreeNode', q: 'TreeNode') -> 'TreeNode':
        return self.dfs(root, p, q)
    
    def dfs(self, root, p, q) -> 'TreeNode':
        if not root:
            return None
        
        if root == p or root == q:
            return root

        leftReturn = self.dfs(root.left, p, q)
        rightReturn = self.dfs(root.right, p, q)
        
        if leftReturn and rightReturn:
            return root
        
        return leftReturn if leftReturn else rightReturn
```

&nbsp;

#### [114. Flatten Binary Tree to Linked List](https://leetcode.com/problems/flatten-binary-tree-to-linked-list/description/)

To flatten the binary tree into a linked list-like binary tree, we should move the left subtree to the right, and move the original right subtree to the position after the end of the original left subtree.

The return from children should track the last one node of the subtree. Upon receiving returns, the current root should follow these steps:
1. `leftReturn.right = root.right`
2. `root.right = root.left`
3. `root.left = None`

Finally, return the last node of the current tree (rightReturn if it exists, otherwise leftReturn, otherwise the root node).

```python
class Solution:
    def flatten(self, root: Optional[TreeNode]) -> None:
        """
        Do not return anything, modify root in-place instead.
        """
        self.dfs(root)
    
    def dfs(self, root: Optional[TreeNode]) -> Optional[TreeNode]:
        if not root:
            return None
        
        leftReturn = self.dfs(root.left)
        rightReturn = self.dfs(root.right)
        
        if not leftReturn and not rightReturn:
            return root

        if leftReturn:
            leftReturn.right = root.right
            root.right = root.left
            root.left = None
            
        return rightReturn if rightReturn else leftReturn     
```

&nbsp;

#### [101. Symmetric Tree](https://leetcode.com/problems/symmetric-tree/description/)

A symmetric tree is symmetric around the root node, so we should consider each pair of subtrees rather than a single subtree. For a pair of subtrees, it is symmetric if:
- The roots are equal.
- The left and right subtrees are symmetric: the left child of the left root equals the right child of the right root, and the right child of the left root equals the left child of the right root.

Thus, we should return a boolean from children and return False if either child returns False or the roots are not equal.

```python
class Solution:
    def isSymmetric(self, root: Optional[TreeNode]) -> bool:
        return self.dfs(root, root)

    def dfs(self, left_root: Optional[TreeNode], right_root: Optional[TreeNode]) -> bool:
        if not left_root and not right_root:
            return True
        
        if not left_root or not right_root or left_root.val != right_root.val:
            return False
        
        return self.dfs(left_root.left, right_root.right) and self.dfs(left_root.right, right_root.left)
```

&nbsp;

## Path Related Types

&nbsp;

#### [257. Binary Tree Paths](https://leetcode.com/problems/binary-tree-paths/description/)

The children should return all paths that have them as the roots. The root should build its paths from leftReturn and rightReturn by adding the root value and `"->"` to each element of them. For a leaf node, directly return its own value string in the list.

```python
class Solution:
    def binaryTreePaths(self, root: Optional[TreeNode]) -> List[str]:
        return self.dfs(root)
        
    def dfs(self, root: Optional[TreeNode]) -> List[str]:
        if not root:
            return []

        leftReturn = self.dfs(root.left)
        rightReturn = self.dfs(root.right)

        if not leftReturn and not rightReturn:
            return [str(root.val)]
        
        paths = []
        for path in leftReturn + rightReturn:
            paths.append(str(root.val) + "->" + path)
        
        return paths
```

&nbsp;

#### [112. Path Sum](https://leetcode.com/problems/path-sum/description/)

The children should return a list of path sums. The root should add its value to each value in the left and right returns to build a new list. The target is found in the root's final list of values.

```python
class Solution:
    def hasPathSum(self, root: Optional[TreeNode], targetSum: int) -> bool:
        pathSums = self.dfs(root)
        if not pathSums:
            return False
        return targetSum in pathSums
       
    def dfs(self, root: Optional[TreeNode]) -> List[int]:
        if not root:
            return []
            
        leftReturn = self.dfs(root.left)
        rightReturn = self.dfs(root.right)
        
        if not leftReturn and not rightReturn:
            return [root.val]
        
        return [(sum + root.val) for sum in (leftReturn + rightReturn)]
```

&nbsp;

#### [549. Binary Tree Longest Consecutive Sequence II](https://leetcode.com/problems/binary-tree-longest-consecutive-sequence-ii/description/)

For a root node, the longest consecutive length can be an extension of the left or right subtree, or it can combine two subtrees as the bridge. The key is to identify the consecutively increasing or decreasing path from the children.

Since the result is the longest length, we can return the longest increase length (ending with the root) and the longest decrease length (starting with the root) from the children. The root should compare several situations' lengths:
1. left increase + 1 (root)
2. left decrease + 1 (root)
3. 1 (root) + right increase
4. 1 (root) + right decrease

At each level, we compare 1 and 3 to get the longest increase length, and compare 2 and 4 to get the longest decrease length. These two values will be passed to the upper level. Additionally, we should calculate `increase + decrease - 1 (root)` to update the longest sequence global variable.

```python
class Solution:
    def longestConsecutive(self, root: Optional[TreeNode]) -> int:
        self.rst = 0
        self.dfs(root)
        return self.rst
        
    def dfs(self, root: Optional[TreeNode]) -> (int, int):
        if not root:
            return 0, 0
        
        left_increase_len, left_decrease_len = self.dfs(root.left)
        right_increase_len, right_decrease_len = self.dfs(root.right)
        
        increase, decrease = 1, 1
        if root.left:
            if root.val - root.left.val == 1:
                increase = left_increase_len + 1
            if root.val - root.left.val == -1:
                decrease = left_decrease_len + 1
        if root.right:
            if root.val - root.right.val == 1:
                increase = max(increase, right_increase_len + 1)
            if root.val - root.right.val == -1:
                decrease = max(decrease, right_decrease_len + 1)
        
        self.rst = max(self.rst, increase + decrease - 1)
        
        return increase, decrease
```

&nbsp;

#### [124. Binary Tree Maximum Path Sum](https://leetcode.com/problems/binary-tree-maximum-path-sum/description/)

Similar to [549. Binary Tree Longest Consecutive Sequence II](https://leetcode.com/problems/binary-tree-longest-consecutive-sequence-ii/description/), the path does not necessarily go through the root. For each level, we should compare not only the path ending at the root, but also the path passing by the root. As node values can be negative, we should compare several situations:
1. root
2. left max
3. left max + root
4. right max
5. right max + root
6. left max + root + right max

At each level, we compare 1, 3, and 5 to get the maximum sum that ends with the root, which will be passed to the upper level. Additionally, we should consider 2, 4, and 6, where the root is not included or included but passed by, to update the maximum sum global variable.

```python
class Solution:
    def maxPathSum(self, root: Optional[TreeNode]) -> int:
        self.rst = float('-inf')
        self.dfs(root)
        return self.rst
        
    def dfs(self, root: Optional[TreeNode]) -> int:
        if not root:
            return float('-inf')
        
        leftReturn = self.dfs(root.left)
        rightReturn = self.dfs(root.right)

        self.rst = max(self.rst, root.val, leftReturn, leftReturn + root.val, rightReturn, rightReturn + root.val, leftReturn + root.val + rightReturn)

        return max(root.val, leftReturn + root.val, rightReturn + root.val)
```

&nbsp;

#### [129. Sum Root to Leaf Numbers](https://leetcode.com/problems/sum-root-to-leaf-numbers/description/)

Similar to [112. Path Sum](https://leetcode.com/problems/path-sum/description/), but recording the path node values rather than adding them together. We can use a list to record nodes in each path and process the list to get the required value. However, an easier way is to record nodes in a path as a string (from root to leaf) and transfer the string directly to the required value.

Children should return a list of strings, each representing a path. The root should add its own value at the beginning of each string.

```python
class Solution:
    def sumNumbers(self, root: Optional[TreeNode]) -> int:
        return sum(int(i) for i in self.dfs(root))
    
    def dfs(self, root: Optional[TreeNode]) -> List[str]:
        if not root:
            return []
        
        if not root.left and not root.right:
            return [str(root.val)]

        leftReturn = self.dfs(root.left)
        rightReturn = self.dfs(root.right)
        
        return [str(root.val) + i for i in (leftReturn + rightReturn)]
```

&nbsp;

## Binary Search Tree (BST) Types

&nbsp;

#### [98. Validate Binary Search Tree](https://leetcode.com/problems/validate-binary-search-tree/description/)

For a valid Binary Search Tree (BST), each root should be larger than the max of the left subtree and smaller than the min of the right subtree. Thus, children should return (min, max) and maintain a global isValid variable. The root should make the comparison and set the variable to False if the condition is invalid.

```python
class Solution:
    def isValidBST(self, root: Optional[TreeNode]) -> bool:
        self.isValid = True
        self.dfs(root)
        return self.isValid
    
    def dfs(self, root: Optional[TreeNode]) -> (int, int):
        if not root:
            return float('inf'), float('-inf')
            
        left_min, left_max = self.dfs(root.left)
        right_min, right_max = self.dfs(root.right)
        
        if root.val <= left_max or root.val >= right_min:
            self.isValid = False
            
        return min(root.val, left_min, right_min), max(root.val, left_max, right_max)       
```

&nbsp;

#### [1373. Maximum Sum BST in Binary Tree](https://leetcode.com/problems/maximum-sum-bst-in-binary-tree/description/)

For a valid Binary Search Tree (BST), each root should be larger than the max of the left subtree and smaller than the min of the right subtree. Thus, children should return min and max of the subtree for validation. They should also return a sum to calculate the new sum. Additionally, we should maintain a global variable to record the maximum sum during DFS. The root should calculate and return new (min, max, sum).

```python
class Solution:
    def maxSumBST(self, root: Optional[TreeNode]) -> int:
        self.rst = float('-inf')
        self.dfs(root)
        return max(0, self.rst)

    def dfs(self, root: Optional[TreeNode]) -> (int, int, int):
        if not root:
            return float('inf'), float('-inf'), 0
        
        left_min, left_max, left_sum = self.dfs(root.left)
        right_min, right_max, right_sum = self.dfs(root.right)

        if left_min is None or right_min is None:
            return None, None, None
        
        if not left_max < root.val < right_min:
            return None, None, None
        
        self.rst = max(self.rst, left_sum + root.val + right_sum)
        return min(left_min, right_min, root.val), max(left_max, right_max, root.val), left_sum + root.val + right_sum
```

&nbsp;

## Summary

The key to using the Divide and Conquer method to solve binary tree-related problems lies in determining what should be returned from the children and how the root should process this return information to build its own return value.

A global variable is optional, but it can be useful for certain problems, especially those involving finding minimum or maximum values (e.g., maximum path sum, maximum average subtree). The global variable helps keep track of the desired value during the recursion.

The corner case happens at the leaf nodes and their children (which are None).Special attention should be given to them, and we should carefully build the return value for these cases, or explicitly indicate return values for leaf nodes (optional).
