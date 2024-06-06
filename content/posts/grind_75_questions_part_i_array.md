---
title: "Grind 75 Questions Part I - Array"
date: 2024-06-05T20:19:35-04:00
description: "Grind 75 \"Array\" part questions covering topics like Double Pointer, Hashmap, DFS, etc."
tags: [LeetCode, Algorithm, Python]
featured_image: "/img/math.jpg"
images: []
categories: LeetCode
comment: true
draft: false
---

## Introduction

[Grind 75](https://www.techinterviewhandbook.org/grind75) is an improved version of [Blind 75](https://www.teamblind.com/post/New-Year-Gift---Curated-List-of-Top-75-LeetCode-Questions-to-Save-Your-Time-OaM1orEU) by the same author. While Blind 75 is a great list to practice coding skills and enter the world of programming algorithms, it includes too few questions and the list is static. 

Grind 75 provides 169 top LeetCode questions (by popularity, frequency, like rating, etc.) and the 75 questions with higher priorities are called new Blind 75, or Grind 75. After completing the top 75 questions, you can go beyond that and solve the remaining questions.

This series plans to cover all 169 questions in Grind 75 as a practice of coding algorithms and preparation for tech interviews. It should be completed within this June. 

For each question, I will provide a thinking process, a brief description of the algorithm, time and space complexity, and then the actual code.

&nbsp;

## [1. Two Sum](https://leetcode.com/problems/two-sum/description/)

The brute force way would be to iteratively check all combinations and calculate their summary, the time complexity of which would be _O(n^2)_.

Each time we go through the array, we only check one pair of numbers and discard all other information. That's why the brute force method is slow.

We can use a hashmap to record more useful information when we go through the array. For each number, we record `target - num` as the key and its index as the value in the hashmap. Later, we can search the hashmap to see whether the current number equals the required number.

The time complexity would be _O(n)_ as we only traverse the array once.

The space complexity would also be _O(n)_ for the created hashmap.

```python
class Solution:
    def twoSum(self, nums: List[int], target: int) -> List[int]:
        targets = defaultdict(int)
        for i, num in enumerate(nums):
            if num not in targets:
                targets[target - num] = i
            else:
                return [targets[num], i]
```

&nbsp;

## [121. Best Time to Buy and Sell Stock](https://leetcode.com/problems/best-time-to-buy-and-sell-stock/)

To get the highest profit, we should find one lowest price and one highest price after the low price. However, if we go through the `prices`, it's not convenient to judge the positions of the lowest and highest prices. 

We can use a `profit` to record current max profit. For each price, we will update the lowest price and calculate the max profit.

The time complexity is _O(n)_ as we only traverse the array once.

The space complexity is _O(1)_ as no extra data structures are created.

```python
class Solution:
    def maxProfit(self, prices: List[int]) -> int:
        lowest = float('inf')
        profit = 0
        for price in prices:
           lowest = min(lowest, price)
           profit = max(profit, price - lowest)
        return profit
```

&nbsp;

## [57. Insert Interval](https://leetcode.com/problems/insert-interval/)

There are several possibilities for the relationship between intervals:
- Interval A outside of interval B: they will become two intervals in the result. They can be identified by comparing right side of interval A and left side of interval B. (or reverse)
- Interval A crossing with interval B: they will merge into one interval
- Interval A contains interval B: they will also merge into one interval (A)

We can go through the `intervals` and compare each interval with the `newInterval`. If merge is needed, directly append the interval to the result. Otherwise, merge it into `newInterval` and append the merged interval.

One issue here is (which is different from [56. Merge Intervals](https://leetcode.com/problems/merge-intervals)) that we should update the `newInterval` before appending it to the result, rather than updating the last interval in the result. In practice, we keep updating (merging) the `newInterval` and append it into the result when the interval has come to the right side of the `newInterval`.

The time complexity is _O(n)_ as we only traverse intervals once.

The space complexity is also _O(n)_ for the new result list.

```python
class Solution:
    def insert(self, intervals: List[List[int]], newInterval: List[int]) -> List[List[int]]:
        rst = []
        placed = False
        for interval in intervals:
            # No overlapping
            if interval[1] < newInterval[0]:
                rst.append(interval)
                
            # Has completed all merging works
            elif interval[0] > newInterval[1]:
                if not placed:
                    rst.append(newInterval)
                    placed = True
                rst.append(interval)
            
            # Merging
            else:
                newInterval = [min(interval[0], newInterval[0]), max(interval[1], newInterval[1])]
        
        # All intervals are smaller than newInterval or are empty
        if not placed:
            rst.append(newInterval)
     
        return rst
```

&nbsp;

## [238. Product of Array Except Self](https://leetcode.com/problems/product-of-array-except-self)

The time is limited to _O(n)_, which means the brute force method wouldn't work (which is _O(n^2)_). Also, the division operation is not allowed, which means we can't get the total product and divide each element one by one.

Each item in `answer` can be calculated by multiplying the product of all prefix items and the product of all suffix items. We can traverse the array two times: the first time to record prefix product at each position, and the second time to record suffix product at each position. Then we build the `answer`. The entire time complexity is limited to 3 rounds of traverse, i.e. _O(n)_.

The space complexity is _O(n)_ as we created a new `answer` array.

```python
class Solution:
    def productExceptSelf(self, nums: List[int]) -> List[int]:
        n = len(nums)
        prefix = [1] * (n + 1)
        suffix = [1] * (n + 1)
        answer = [1] * n
        
        for i in range(n):
            prefix[i + 1] = prefix[i] * nums[i]
        for i in range(n - 1, 0, -1):
            suffix[i - 1] = suffix[i] * nums[i]
        for i in range(n):
            answer[i] = prefix[i] * suffix[i]
            
        return answer
```

&nbsp;

## [39. Combination Sum](https://leetcode.com/problems/combination-sum)

As each number can be chosen an unlimited number of times, which means for each step, we can choose the next number from all the numbers in the array. This creates a tree where each node has all numbers as its children. 

As we want all possible combinations, we can use DFS to get one combination and backtrack to get all possible ones. We should keep track of the current path using `temp`, validate it at each step, and record valid combinations in `res` list.

- If the sum of `temp` equals the target, we should record it in `res` and backtrack to the upper level. 
- If the sum of `temp` exceeds the target, which means this path can't work, we should backtrack directly to the upper level.
- If the sum of `temp` is less than the target, we should append current node to `temp` and explore the next level. When the next level returns, it means the path has been searched, and we should pop the current node and move to the next node.  

The time complexity is _O(n^l)_, where _n_ is the length of array and _l_ is the longest path (target / minInArray). In the worst case, we need to visit a tree of _l_ levels and the bottom level would have _n*l_ choices. Each level have _n*level_ choices, and the sum would be _O(n^l)_.

The space complexity would be the _O(n*l)_. The bottom level occupies _n*l_ nodes. When backtracking, the space is freed and the upper level occupies less space. So the most space needed is _O(n*l)_.
 
```python
class Solution:
    def combinationSum(self, candidates: List[int], target: int) -> List[List[int]]:
        res = []
        def dfs(candidates, temp, target, startIndex):
            if sum(temp) == target:
                res.append(temp + [])  # deepcopy
                return
            if sum(temp) > target:
                return
            for i in range(startIndex, len(candidates)):
                temp.append(candidates[i])
                dfs(candidates, temp, target, i)
                temp.pop()
        dfs(candidates, [], target, 0)
        return res  
```

&nbsp;

## [56. Merge Intervals](https://leetcode.com/problems/merge-intervals)

There are several possibilities for the relationship between intervals: 
- Interval A outside of interval B: they will become two intervals in the result. They can be identified by comparing right side of interval A and left side of interval B. (or reverse)
- Interval A crossing with interval B: they will merge into one interval
- Interval A containing interval B: they will also merge into one interval (A)

If we sort the intervals (note the given array is not sorted), go through each interval, and compare it with the last interval in the result, we can decide whether to merge it into the last interval or append it into the result directly.

The time complexity is _O(nlogn) + O(n) = O(nlogn)_ for sorting.

The space complexity is _O(1)_ as no extra data structures are created.

```python
class Solution:
    def merge(self, intervals: List[List[int]]) -> List[List[int]]:
        intervals.sort()
        res = []
        for interval in intervals:
            if len(res) == 0 or interval[0] > res[-1][1]:
                res.append(interval)
            else:
                res[-1][0] = min(interval[0], res[-1][0])
                res[-1][1] = max(interval[1], res[-1][1])
        return res    
```

&nbsp;

## [169. Majority Element](https://leetcode.com/problems/majority-element)

The straightforward way would be to use a hashmap to record the counts of elements and return the element once its count exceeds _n/2_.

The time complexity is _O(n)_, as we traverse the list.

The space complexity is _O(n)_, as the hashmap would store at most n items (unique list).

```python
class Solution:
    def majorityElement(self, nums: List[int]) -> int:
        counts = defaultdict(int)
        for num in nums:
            counts[num] += 1
            if counts[num] > len(nums) / 2:
                return num
        return -1
```

There is also an easier solution: after sorting the array, the majority element with more than _n/2_ counts would always appear at the middle point of the array. The time complexity is _O(nlogn)_ for sorting, and the space complexity is _O(1)_.

```python
class Solution:
    def majorityElement(self, nums: List[int]) -> int:
        nums.sort()
        return nums[len(nums) // 2]
```

&nbsp;

## [75. Sort Colors](https://leetcode.com/problems/sort-colors)

This is the problem of Dutch Flag, the key is to put two different colors at the two sides and leave the 3rd color in the middle.

As the question requires in-place sorting, swapping should be applied. There are two moving edges (pointers) between the 3 colors, and swapping happens at these 2 edges. This can also be seen as a two-pointer problem. In addition, there should be a 3rd pointer going through the list to check values of items.

Swapping current with the left side would make the current element a smaller one, while swapping current element with the right side would make current an un-known one. Thus, the iterating index should not increase when swapping with right side, and we should check the new current element again. For the middle color, just increase the iterating index without swapping.

The time complexity is _O(n)_, as all three pointers traverse at most twice the entire list.

The space complexity is _O(1)_, as no new data structures are created.

```python
class Solution:
    def sortColors(self, nums: List[int]) -> None:
        """
        Do not return anything, modify nums in-place instead.
        """
        left = 0
        right = len(nums) - 1
        while i < right:
            if nums[i] == 0:
                nums[left], nums[i] = nums[i], nums[left]
                left += 1
                i += 1
            elif nums[i] == 2:
                nums[right], nums[i] = nums[i], nums[right]
                right -= 1
            else:
                i += 1      
```

There is also an easier solution: as there are mostly 3 kinds of items. We can count the number of each item, and directly assign values to the original list based on the count numbers. The time complexity is still _O(n)_, but the space complexity becomes _O(n)_ for the new hashmap.

```python
class Solution:
    def sortColors(self, nums: List[int]) -> None:
        """
        Do not return anything, modify nums in-place instead.
        """
        counts = defaultdict(int)
        for num in nums:
            counts[num] += 1
       
        for i in range(len(nums)):
            if i < counts[0]:
                nums[i] = 0
            elif i < counts[0] + counts[1]:
                nums[i] = 1
            else:
                nums[i] = 2
            
```

&nbsp;

## [217. Contains Duplicate](https://leetcode.com/problems/contains-duplicate)

Use a hashmap to record the appearance of items. Return false when encounter the 2nd appearance of an item. 

The time complexity is _O(n)_ due to the iteration through the list.

The space complexity is _O(n)_ for the new hashmap.

```python
class Solution:
    def containsDuplicate(self, nums: List[int]) -> bool:
        counts = defaultdict(int)
        for num in nums:
            counts[num] += 1
            if counts[num] == 2:
                return True
        return False
```

&nbsp;

## [11. Container With Most Water](https://leetcode.com/problems/container-with-most-water)

There are two moving endpoints (pointers) of the container, and the shorter line limits the size of the container. The issue with two pointers is: which one is fixed and which one is floating. For each step, the floating one should always move in a direction that can lead to a better result.

If two pointers start from the left side or from some point within the array, expanding the width will not necessarily lead to a larger container (neither the short nor the longer line). However, shrinking the longer line will always lead to a smaller container (either renewing the shorter line or keeping the original shorter line but with a smaller width).

Thus, we can start from two sides of the array and always shrink the shorter line. This will not guarantee a better result at each step, but it will have a chance to achieve one.

The time complexity is _O(n)_, as the left and right pointers traverse the entire list together.

The space complexity is _O(1)_, as no new structures are created.

```python
class Solution:
    def maxArea(self, height: List[int]) -> int:
        left = 0
        right = len(height) - 1
        max_area = 0
              
        while left < right:
            smaller_height = min(height[left], height[right])
            width = right - left
            max_area = max(max_area, smaller_height * width)
            if height[left] < height[right]:
                left += 1
            else:
                right -= 1
        
        return max_area
```
