---
title: "Common LeetCode Algorithms Part I: Binary Search"
date: 2024-06-07T16:50:25-04:00
description: "Binary Search is an efficient algorithm aiming to find and item in ordered or partially-ordered array within logarithmic time."
tags: [LeetCode, Algorithm, Python]
featured_image: "/img/math.jpg"
images: []
categories: LeetCode
comment: true
draft: false
---

## Introduction

Binary Search is an efficient algorithm for finding an item in an ordered or partially ordered array with a logarithmic time complexity of _O(logn)_. It works by repeatedly dividing the search range in half, comparing the middle element to the target value, and discarding the half that does not include the target. This process is repeated until the target is found or the search range is exhausted. 

Binary Search is significantly faster than linear search algorithms, which have a time complexity of _O(n)_. It is commonly used in problems that require finding a specific item or the first/last occurrence of a required item in a sorted array.

&nbsp;

## Binary Search Pattern

- **Applications**: An array is **ordered** in some range.
- **Purpose**: TO **shrink** the searching range. 
- **Keys**:
  - What is the **search range**? 
  - What is the **target**?

```python
def search(self, nums: List[int], target: int) -> int:
    start = 0
    end = len(nums) - 1
    
    # Make sure there are exactly 2 items at the end
    # to avoid possible dead loops when using 'start < end'
    while start + 1 < end:  
        mid = (start + end) // 2
        if nums[mid] < target:
            start = mid
        elif nums[mid] > target:
            end = mid
        else:
            return mid
    
    # Check the remaining 2 items
    if nums[start] == target:
        return start
    
    if nums[end] == target:
        return end
    
    # Target not found
    return -1
```

&nbsp;

## Normal Type

This type of problem may lack some conditions required for running a Binary Search, such as an accurate search range (upper or lower limit) or an accurate target. The key idea is to identify or build the necessary conditions to run a Binary Search.

&nbsp;

#### [704. Binary Search](https://leetcode.com/problems/binary-search/description/)

```python
class Solution:
    def search(self, nums: List[int], target: int) -> int:
        start = 0
        end = len(nums) - 1
        
        while start + 1 < end:
            mid = (start + end) // 2
            if nums[mid] < target:
                start = mid
            elif nums[mid] > target:
                end = mid
            else:
                return mid
        
        if nums[start] == target:
            return start
        if nums[end] == target:
            return end
        return -1
```

&nbsp;

#### [702. Search in a Sorted Array of Unknown Size](https://leetcode.com/problems/search-in-a-sorted-array-of-unknown-size/description/)

An unknown size means an unknown upper limit of the search range. To find the accurate search range without affecting the total time complexity, we should do it in _O(logn)_ time.

The lower limit is certain; we can start from 0 and 1 to check whether the target is in this range. If not, move to the right while doubling the range. This way, we can find the accurate range in _O(logn)_ time.

After finding the accurate range, we can execute a binary search. The total time complexity is still _O(logn)_. The space complexity is _O(1)_ as no extra data structures are created.

```python
class Solution:
    def search(self, reader: 'ArrayReader', target: int) -> int:
        # Find accurate search range
        start = 0
        end = 1
        while reader.get(end) <= target:
            start = end
            end = end * 2
        
        # Binary search
        while start + 1 < end:
            mid = (start + end) // 2
            if reader.get(mid) < target:
                start = mid
            elif reader.get(mid) > target:
                end = mid
            else:
                return mid
        
        if reader.get(start) == target:
            return start
        if reader.get(end) == target:
            return end
        return -1
```

&nbsp;

#### [74. Search a 2D Matrix](https://leetcode.com/problems/search-a-2d-matrix/description/)

In a normal binary search, an element can be selected given an index of the array. For a 2D matrix (which should be ordered), the key is to give an index and extract the corresponding number from the matrix.

Here we build a function to transfer one index number to row and column numbers of the matrix and get the corresponding integer in the matrix. Passing `start`, `end`, `mid`, etc. to the function can return an integer in the matrix.

The time complexity and space complexity are still the same as a normal Binary Search as this transformation is a pure math calculation (_O(1)_).

```python
class Solution:
    def searchMatrix(self, matrix: List[List[int]], target: int) -> bool:
        start = 0
        end = len(matrix[0]) * len(matrix) - 1
        while start + 1 < end:
            mid = (start + end) // 2
            if self.getIntFromMatrix(matrix, mid) < target:
                start = mid
            elif self.getIntFromMatrix(matrix, mid) > target:
                end = mid
            else:
                return True
            
        if self.getIntFromMatrix(matrix, start) == target or self.getIntFromMatrix(matrix, end) == target:
            return True
        
        return False
        
        
    def getIntFromMatrix(self, matrix: List[List[int]], index: int) -> int:
        columns = len(matrix[0])
        row = index // columns
        col = index % columns
        return matrix[row][col]
```

&nbsp;

#### [240. Search a 2D Matrix II](https://leetcode.com/problems/search-a-2d-matrix-ii/description/)

Unlike [74. Search a 2D Matrix](https://leetcode.com/problems/search-a-2d-matrix/description/), this matrix is not ordered. Binary search requires strict ordering in some range since, for each element, we need to identify which half should be discarded.

For each element in this matrix, if we move to the larger side, there could be two directions (going down or going right), making it less efficient. If we go through all two different directions, in the worst case (brute force method), we need to visit every cell in the matrix, resulting in a time complexity of _O(mn)_.

However, there are two special cells: the upper right and lower left ones. Comparing them with the target and deciding to move to the larger side leaves only one direction to go.

If we start from the upper right corner (or lower left corner) and move the pointer based on the comparison with the target, there is only one direction to go in each step. The time complexity would be _O(m+n)_ (from one corner to another corner in the worst case). The space complexity is _O(1)_ as no extra data structures are created.

```python
class Solution:
    def searchMatrix(self, matrix: List[List[int]], target: int) -> bool:
        row = 0
        col = len(matrix[0]) - 1
        while row <= len(matrix) - 1 and col >= 0:
            if matrix[row][col] < target:
                row += 1
            elif matrix[row][col] > target:
                col -= 1
            else:
                return True
        
        return False              
```

&nbsp;

## OOXX Type

This type of question does not aim to find the element exactly the same as the target. It may demand us to find "the first [condition] number" or the "last not [condition] number" in a sorted array. It's like an array of "O" that becomes "X" from some point, and we want to find that point.

These questions are basically still Binary Search problems, but the validation method is slightly different. When the `mid` element equals the target, the function should continue to search rather than directly return. The key is to transfer the original question to questions like "find the first [condition] one".

&nbsp;

#### [34. Find First and Last Position of Element in Sorted Array](https://leetcode.com/problems/find-first-and-last-position-of-element-in-sorted-array/description/)

The problem can be split into two runs of Binary Search: one for the first position and one for the last position. The difference from a normal Binary Search is that when `nums[mid] == target`, it doesn't return directly but continues to search the left half (to find the first) or the right half (to find the last). Another difference is when validating the remaining two elements, the `end` should be checked first to find the last position as the remaining two elements could be the same.

The time complexity is still _O(logn)_ for two rounds of Binary Search. And the space complexity is still _O(1)_ as no extra data structures are created.

```python
class Solution:
    def searchRange(self, nums: List[int], target: int) -> List[int]:
        if not nums:  # Note nums may be empty according to the constraint
            return [-1, -1]  
        return [self.searchFirst(nums, target), self.searchLast(nums, target)]

    def searchFirst(self, nums: List[int], target: int) -> int:
        start = 0
        end = len(nums) - 1
        while start + 1 < end:
            mid = (start + end) // 2
            if nums[mid] < target:
                start = mid
            else:
                end = mid

        if nums[start] == target:
            return start
        if nums[end] == target:
            return end
        return -1
    
    def searchLast(self, nums: List[int], target: int) -> int:
        start = 0
        end = len(nums) - 1
        while start + 1 < end:
            mid = (start + end) // 2
            if nums[mid] <= target:
                start = mid
            else:
                end = mid

        if nums[end] == target:
            return end
        if nums[start] == target:
            return start
        return -1
```

&nbsp;

#### [35. Search Insert Position](https://leetcode.com/problems/search-insert-position/description/)

The problem can be explained as "find the first number greater than the target" or "find the last number less than the target." The former is easier to manipulate as the answer is exactly the number's index.

The difference from a normal Binary Search is the validation of the remaining two elements. Instead of equaling the target, greater than the target still is a valid one in this problem. If the target is greater than the remaining two, it means the target is greater than the entire `nums` and should be inserted after the last element.

The time and space complexity are the same as a normal Binary Search (_O(logn)_ and _O(1)_).

```python
class Solution:
    def searchInsert(self, nums: List[int], target: int) -> int:
        start = 0
        end = len(nums) - 1
        while start + 1 < end:
            mid = (start + end) // 2
            if nums[mid] < target:
                start = mid
            elif nums[mid] > target:
                end = mid
            else:
                return mid
        
        if nums[start] >= target:  # insert at start
            return start
        if nums[end] >= target:  # insert at end
            return end
        return len(nums)  # the remaining 2 is right most and both less than target 
```

&nbsp;

#### [162. Find Peak Element](https://leetcode.com/problems/find-peak-element/description/)

Peak elements mean the array has some increasing range (at least the one before the peak) and some decreasing range (at least the one after the peak). Binary Search can be applied with some-range ordered array. However, this problem has no target. The key is how to drop which half for a given element.

If we find an element, it may be: the peak, elements before the peak, or elements after the peak. Elements before the peak should have a positive slope, while elements after the peak should have a negative slope. We can compare the element with its neighbor to find its slope. This way, we can decide the moving direction during the Binary Search.

The time and space complexity are the same as a normal Binary Search (_O(logn)_ and _O(1)_).

```python
class Solution:
    def findPeakElement(self, nums: List[int]) -> int:
        start = 0
        end = len(nums) - 1
        while start + 1 < end:
            mid = (start + end) // 2
            if nums[mid] > nums[mid - 1]:
                start = mid
            else:
                end = mid
        
        if nums[start] > nums[end]:
            return start
        else:
            return end       
```

&nbsp;

#### [278. First Bad Version](https://leetcode.com/problems/first-bad-version/description/)

A typical "OOXX" type problem. The purpose is to find the first element that doesn't meet a certain condition (`isBadVersion`) or the next one after the last element that meets a certain condition.

The difference from a normal Binary Search is it will not return early but always goes to the last two elements. Note to find the first bad version, we should check `start` before `end` (corner case is the remaining two elements are the same).

The time and space complexity are the same as a normal Binary Search (_O(logn)_ and _O(1)_).

```python
class Solution:
    def firstBadVersion(self, n: int) -> int:
        start = 1
        end = n
        while start + 1 < end:
            mid = (start + end) // 2
            if isBadVersion(mid):
                end = mid
            else:
                start = mid
        
        if isBadVersion(start):
            return start
        else:
            return end
```

&nbsp;

#### [153. Find Minimum in Rotated Sorted Array](https://leetcode.com/problems/find-minimum-in-rotated-sorted-array/description/)

The rotated array has two increasing ranges. The target is to find the minimum value, which should be found in the range below the rotated value. For a given element (`mid`), one half would be strictly increasing, and the other half will not, which contains our target.

We can compare the element with the rotated value to judge which half is the ordered one. Then search in the other half.

The time and space complexity are the same as a normal Binary Search (_O(logn)_ and _O(1)_).

```python
class Solution:
    def findMin(self, nums: List[int]) -> int:
        start = 0
        end = len(nums) - 1
        while start + 1 < end:
            mid = (start + end) // 2
            if nums[mid] > nums[end]:  # nums[0] will not work for a not rotated range
                start = mid
            else:
                end = mid
        
        return min(nums[start], nums[end]) 
```

&nbsp;

#### [33. Search in Rotated Sorted Array](https://leetcode.com/problems/search-in-rotated-sorted-array/description/)

Similar to [153. Find Minimum in Rotated Sorted Array](https://leetcode.com/problems/find-minimum-in-rotated-sorted-array/description/). However, for a given element (`mid`), it is not easy to judge which half should be discarded by comparing it with the target. If the element is within the left increasing range (above the rotated value), then we should discard the left half if we want to move up. However, if we want to move down to smaller elements, they may exist among both left and right halves.

We can compare the element not only with the target but also with the rotated value (`nums[0]`) to judge whether the element is within the ordered left half. If the element is within the right increasing range, we should follow a similar approach.

The time and space complexity are the same as a normal Binary Search (_O(logn)_ and _O(1)_).

```python
class Solution:
    def search(self, nums: List[int], target: int) -> int:
        start = 0
        end = len(nums) - 1
        while start + 1 < end:
            mid = (start + end) // 2
            if nums[mid] >= nums[0]:  # above rotated value
                if nums[0] <= target <= nums[mid]:  # target within the left increasing half
                    end = mid
                else:
                    start = mid
            else:  # below rotated value
                if nums[mid] <= target <= nums[end]:  # target within the right increasing half
                    start = mid
                else:
                    end = mid
                    
        if nums[start] == target:
            return start
        if nums[end] == target:
            return end
        return -1   
```

&nbsp;

## Binary Answer Type

This type of question do not have a clear search range. And always the judging condition is not the required variable (search variable). 

The key is to identify:
- The upper and lower limits
- The function to transform the search variable to the condition variable
- The condition to judge and discard one half

&nbsp;

#### [69. Sqrt(x)](https://leetcode.com/problems/sqrtx/description/)

The square root of `x` cannot exceed `x`. So the upper limit is `x` and the lower limit is 0. For a given element (`mid`), we can compare `mid * mid` with `x` to judge which half we should go.

Since the problem asks to round the result down to the nearest integer, for the remaining two elements, we should consider the larger one first. And whether the smaller one meets the target or not (no integer result), we will return it.

The time and space complexity are the same as a normal Binary Search (_O(logn)_ and _O(1)_).

```python
class Solution:
    def mySqrt(self, x: int) -> int:
        start = 0
        end = x
        
        while start + 1 < end:
            mid = (start + end) // 2
            if mid * mid < x:
                start = mid
            elif mid * mid > x:
                end = mid
            else:
                return mid
        
        if end * end == x:
            return end
        else:
            return start  # since we round it down to nearest integer
```

&nbsp;

#### [875. Koko Eating Bananas](https://leetcode.com/problems/koko-eating-bananas/description/)

The lower limit is 1, as Koko needs to eat at least 1 banana each hour; otherwise, it cannot complete the task. The upper limit is `max(piles)`, as the total time would not change if Koko eats more than the `max(piles)` per hour: it has to wait for the hour to finish before moving to the next pile of bananas.

For a given element (`mid`), we can check whether the `mid` speed can eat all bananas within `h`. For the remaining two elements, we should consider the left one first, as we want a slower speed. As the problem assumes Koko can always eat all bananas, there is no need to consider -1.

The time and space complexity are the same as a normal Binary Search (_O(logn)_ and _O(1)_).

```python
class Solution:
    def minEatingSpeed(self, piles: List[int], h: int) -> int:
        start = 1
        end = max(piles)
        
        while start + 1 < end:
            mid = (start + end) // 2
            if self.eatingTime(piles, mid) <= h:
                end = mid
            else:
                start = mid
        
        if self.eatingTime(piles, start) <= h:
            return start
        else:
            return end 
        
    def eatingTime(self, piles: List[int], k: int) -> int:
        h = 0
        for pile in piles:
            h += pile // k
            if pile % k != 0:
                h += 1
        return h
```

&nbsp;

#### [1060. Missing Element in Sorted Array](https://leetcode.com/problems/missing-element-in-sorted-array/description/)

The upper and lower limits are very clear, but it is not clear how to judge and drop one half for a given element (`mid`). For each element, the number of missing items before it can be calculated by `nums[i] - nums[0] - i`. Thus, we can compare this value with `k` and locate the position where the result should be found.

The time and space complexity are the same as a normal Binary Search (_O(logn)_ and _O(1)_).

```python
class Solution:
    def missingElement(self, nums: List[int], k: int) -> int:
        start = 0
        end = len(nums) - 1
        
        while start + 1 < end:
            mid = (start + end) // 2
            if self.getMissingCount(nums, mid) < k:
                start = mid
            else:
                end = mid
        
        if self.getMissingCount(nums, end) < k:
            return nums[end] + k - self.getMissingCount(nums, end)
        else:
            return nums[start] + k - self.getMissingCount(nums, start)
    
    def getMissingCount(self, nums: List[int], i: int) -> int:
        return nums[i] - nums[0] - i
```

&nbsp;

#### [Amazon OA 2023 Dec.]()

> Amazon Warehouse delivers different items in different trucks having varied capacities.
Given an array, trucks, of n integers that represents the capacities of different trucks, and an array items, of m integers that represent the weights of different items, for each item, find the index of the smallest truck which has a capacity greater than the item's weight. If there are multiple such trucks, choose the one with the minimum index.
If there is no truck that can carry the item, report -1 as the answer for the corresponding item.
> 
> Note: Assume that the trucks are indexed starting from 0. Also, multiple items can be mapped to the same truck. Each item is mapped independently, hence the trucks do not lose any capacity when a particular item is mapped to it.
>
> Example：Suppose, trucks = [4, 5, 7, 2] and items = [1, 2, 5]. Output should be [3, 0, 2]

Finding the first element greater than a given value fits the pattern of Binary Search. However, the trucks must be sorted before executing a Binary Search. Since the result is the trucks' indices, we should also record the trucks' original indices during sorting (A list of tuples can do this).

On the sorted trucks, Binary Search can be executed. Note that for a truck whose capacity is greater than the required one, we should still search its left side as we want to find the one with the minimum index.

The time complexity should be _O(nlogn) + O(mlogn) = O((m+n)logn)_ due to the sorting operation. The space complexity is _O(n)_ for the created list of sorted trucks.

```python
class Solution:
    def findMinTruck(trucks: List[int], items: List[int]) -> List[int]:
        sorted_trucks = sorted([(truck, index) for index, truck in enumerate(trucks)])
        rst = []
        for item in items:
            start = 0
            end = len(sorted_trucks) - 1
            while start + 1 < end:
                mid = (start + end) // 2
                if sorted_trucks[mid][0] > item:
                    end = mid
                else:
                    start = mid
   
            if sorted_trucks[start][0] > item:
                rst.append(sorted_trucks[start][1])
            elif sorted_trucks[end][0] > item:
                rst.append(sorted_trucks[end][1])
            else:
                rst.append(-1)
        
        return rst
```

&nbsp;

#### [Integer that Value Equals Index]()
> For a strictly ordered integer array `nums`, find an index `i` making `nums[i] == i`. If such an index can't be found, return -1.
> 
> Example 1: nums = [-5 -2 1 3 5], output = 3
> 
> Example 2: nums = [-5, -2, 1, 4, 5, 6, 7, 8, 9], output = -1

The upper and lower limits are clear. However, for a given element (mid), it's not clear how to judge which half should be discarded.

The indices are also a list, where each element increases strictly by 1 compared to its previous element. In contrast, the values in the given array may increase by more than 1 compared to the previous value. Thus, if one element is greater than its index, all elements after it cannot have the same values as their indices. Similarly, if one element is smaller than its index, all elements before it cannot have the same values as their indices. This way, we can shrink the search range by half.

The time and space complexity are the same as a normal Binary Search (_O(logn)_ and _O(1)_).

```python
class Solution:
    def findIntegerEqualsIndex(nums: List[int]) -> int:
        start = 0
        end = len(nums) - 1
        while start + 1 < end:
            mid = (start + end) // 2
            if nums[mid] > mid:
                end = mid
            elif nums[mid] < mid:
                start = mid
            else:
                return mid
        
        if nums[start] == mid:
            return start
        if nums[end] == mid:
            return end
        return -1        
```

&nbsp;

#### [1011. Capacity To Ship Packages Within D Days](https://leetcode.com/problems/capacity-to-ship-packages-within-d-days/description/)

```python
class Solution:
    def shipWithinDays(self, weights: List[int], days: int) -> int:
        start = max(weights)
        end = sum(weights)
        while start + 1 < end:
            mid = (start + end) // 2
            if self.canShip(weights, days, mid):
                end = mid
            else:
                start = mid
        
        if self.canShip(weights, days, start):
            return start
        else:
            return end
    
    def canShip(self, weights: List[int], days: int, capacity: int) -> bool:
        curr_weights = 0
        curr_days = 0

        for weight in weights:
            if weight > capacity:
                return False
            
            if weight <= capacity - curr_weights:
                curr_weights += weight
            else:
                curr_days += 1
                curr_weights = weight
        
        if curr_weights > 0:
            curr_days += 1

        return curr_days <= days
```

&nbsp;

#### [410. Split Array Largest Sum](https://leetcode.com/problems/split-array-largest-sum/description/)

```python
class Solution:
    def splitArray(self, nums: List[int], k: int) -> int:
        # upper and lower limits of max_sum
        start = max(nums)  # each element as a subarray (k=n)
        end = sum(nums)  # only one subarray (k=1)
        while start + 1 < end:
            mid = (start + end) // 2
            if self.countSubarrays(nums, mid) <= k:
                end = mid
            else:
                start = mid

        if self.countSubarrays(nums, start) <= k:
            return start
        else:
            return end
    
    # This result is the least subarray count
    # We can always split into more subarrays, e.g. each element as one subarray
    def countSubarrays(self, nums: List[int], max_sum: int) -> int:
        curr_count = 0
        curr_sum = 0

        for num in nums:
            if curr_sum <= max_sum - num:
                curr_sum += num
            else:
                curr_count += 1
                curr_sum = num
    
        if curr_sum > 0:
            curr_count += 1
        return curr_count
```

&nbsp;

#### [774. Minimize Max Distance to Gas Station](https://leetcode.com/problems/minimize-max-distance-to-gas-station/description/)

```python
class Solution:
    def minmaxGasDist(self, stations: List[int], k: int) -> float:
        distances = [y - x for x, y in zip(stations, stations[1:])]   
        start = 1e-6  # k = inf
        end = max(distances)  # k = 0
        while start + 1e-6 < end:
            mid = (start + end) / 2. # use / instead of // as mid can be float
            if self.getMaxK(distances, mid) <= k:
                end = mid
            else:
                start = mid
        
        if self.getMaxK(distances, start) >= k:
            return start
        else:
            return end
    
    def getMaxK(self, distances: List[int], max_distance: float) -> int:
        return sum([d // max_distance for d in distances])
```

&nbsp;

#### [475. Heaters](https://leetcode.com/problems/heaters/description/)

```python
class Solution:
    def findRadius(self, houses: List[int], heaters: List[int]) -> int:
        houses.sort()
        heaters.sort()
        
        start = 0
        end = max(houses[-1], heaters[-1]) - min(houses[0], heaters[0])
        while start + 1 < end:
            mid = (start + end) // 2
            if self.canCover(houses, heaters, mid):
                end = mid
            else:
                start = mid
        
        if self.canCover(houses, heaters, start):
            return start
        else:
            return end
    
    def canCover(self, houses: List[int], heaters: List[int], radius: int) -> bool:
        i = 0
        j = 0
        while i < len(houses) and j < len(heaters):
            if abs(houses[i] - heaters[j]) <= radius:
                i += 1
            else:
                j += 1
        
        return i == len(houses)
```

```python
class Solution:
    def findRadius(self, houses: List[int], heaters: List[int]) -> int:
        heaters.sort()
        return max([self.nearestHeaterDist(heaters, house) for house in houses])
    
    def nearestHeaterDist(self, heaters: List[int], house: int) -> int:
        start = 0
        end = len(heaters) - 1
        while start + 1 < end:
            mid = (start + end) // 2
            if heaters[mid] < house:
                start = mid
            elif heaters[mid] > house:
                end = mid
            else:
                return 0

        return min(abs(heaters[start] - house), abs(heaters[end] - house))
```

&nbsp;

#### [1231. Divide Chocolate](https://leetcode.com/problems/divide-chocolate/description/)

```python
class Solution:
    def maximizeSweetness(self, sweetness: List[int], k: int) -> int:
        start = min(sweetness)  # k = len(sweetness) at most
        end = sum(sweetness)  # k = 1 at most
        while start + 1 < end:
            mid = (start + end) // 2
            if self.getMaxK(sweetness, mid) < k:
                end = mid
            else:
                start = mid
   
        if self.getMaxK(sweetness, end) >= k:
            return end
        else:
            return start
    
    def getMaxK(self, sweetness: List[int], min_sweet: int) -> int:
        curr_sweet = 0
        curr_count = 0
        for sweet in sweetness:
            if curr_sweet + sweet >= min_sweet:
                curr_count += 1
                curr_sweet = 0
            else:
                curr_sweet += sweet
        
        return curr_count - 1  # k pieces means k-1 cuts
```

&nbsp;

#### [981. Time Based Key-Value Store](https://leetcode.com/problems/time-based-key-value-store/description/)

```python
class TimeMap:
    def __init__(self):
        self.store = {}
        
    def set(self, key: string, value: string, timestamp: int) -> None:
        if key not in self.store:
            self.store[key] = []
        self.store[key].append((timestamp, value))
    
    def get(self, key: string, timestamp: int) -> string:
        if key not in self.store:
            return ""
        set_list = self.store[key]
        start = 0
        end = len(set_list) - 1
        while start + 1 < end:
            mid = (start + end) // 2
            if set_list[mid][0] <= timestamp:
                start = mid
            else:
                end = mid
        
        if set_list[end][0] <= timestamp:
            return set_list[end][1]
        if set_list[start][0] <= timestamp:
            return set_list[start][1]
        return ""      
```

&nbsp;

## Summary

Normal binary search questions typically involve identifying the shrinking direction by directly comparing the result variable with the target. Some questions may lack clear search limits or a specific target, in which case we should try to locate the result variable by comparing it with different intervals.

OOXX type questions are a variant of binary search. The key difference is that in a normal binary search, the search stops and returns when the result variable equals the target. In contrast, OOXX type questions continue to search in such cases. These questions usually aim to "find the first element with [condition]" or "find the last element without [condition]."

Binary answer type questions are the most challenging form of binary search. In these problems, the result variable cannot be directly compared with the target. Instead, methods or functions must be identified to transform the result variable into the target variable, where binary search can be applied. Additionally, the upper and lower limits are usually not clear in binary answer type questions. Splitting subarrays is a typical example of this type, where the upper and lower limits often refer to the smallest and largest possible splits.
