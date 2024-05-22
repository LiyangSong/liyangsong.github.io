---
title: "LeetCode SQL50 Part I - Select"
date: 2024-05-20T14:42:45-04:00
description: "The analysis and answers to LeetCode SQL50 \"Select\" part questions. Both SQL and Pandas methods are used."
tags: [LeetCode, MySQL, SQL, Pandas, Python]
featured_image: "/img/hard_drive.jpg"
images: []
categories: LeetCode
comment: true
draft: false
---

## Introduction

LeetCode SQL50 contain 50 basic to intermediate SQL questions covering 7 SQL topics.

This is the `Select` part of SQL50, which mainly focus on conditional selection using `MySQL` and `Pandas`.

&nbsp;

## [1757. Recyclable and Low Fat Products](https://leetcode.com/problems/recyclable-and-low-fat-products/)

**MySQL**

Two-condition select, directly use `WHERE` `AND`.

```mysql
SELECT product_id FROM Products
WHERE low_fats = 'Y' AND recyclable = 'Y'
```

**Pandas**

Can use bracket select or `.loc` select of Pandas DataFrame. 

**Note the conditions in bracket should use Series logic operators `&`, `|`, `~` rather than AND, OR, NOT in python.** 

Note the return type should be DataFrame (double bracket) rather thank Series (single bracket).

```python
products[(products['low_fats'] == 'Y') & (products['recyclable'] == 'Y')][['product_id']]
```

```python
products.loc[(products['low_fats'] == 'Y') & (products['recyclable'] == 'Y'), ['product_id']]
```

&nbsp;

## [584. Find Customer Referee](https://leetcode.com/problems/find-customer-referee/)

**MySQL**

Two-condition select, directly use `WHERE` `OR`.

```mysql
SELECT name from Customer
WHERE referee_id is null OR referee_id != 2
```

**Pandas**

Use `~` and `isin()` of Pandas Series. 

```python
customer[~customer['referee_id'].isin(['null', 2])][['name']]
```

&nbsp;

## [595. Big Countries](https://leetcode.com/problems/big-countries)

**MySQL**

Two-condition select, directly use `WHERE` `OR`.

```mysql
SELECT name, population, area FROM World
WHERE area >= 3000000 OR population >= 25000000
```

**Pandas**

Use `｜` Pandas Series.

```python
world[(world['area'] >= 3_000_000) | (world['population'] >= 25_000_000)][['name', 'population', 'area']]
```

&nbsp;

## [1148. Article Views I](https://leetcode.com/problems/article-views-i/)

**MySQL**

Use `SELECT DISTINCT` as there are duplicates. User `ORDER BY` to sort.

```mysql
SELECT DISTINCT author_id AS id FROM Views
WHERE author_id = viewer_id
ORDER BY id
```

**Pandas**

Use bracket select to filter by the requirement.

**Note `drop_duplicates`, `rename`, `sort_values` all have `inplace` argument.**

```python
def article_views(views: pd.DataFrame) -> pd.DataFrame:
    views = views[views['author_id'] == views['viewer_id']][['author_id']]
    views.drop_duplicates(inplace=True)
    views.rename(columns={'author_id': 'id'}, inplace=True)
    views.sort_values('id', inplace=True)
    return views
```

&nbsp;

## [1683. Invalid Tweets](https://leetcode.com/problems/invalid-tweets/)

**MySQL**

Directly use `LENGTH()`.

```mysql
SELECT tweet_id FROM Tweets
WHERE LENGTH(content) > 15
```

**Pandas**

Use `.str.len()` method of Pandas Series, which return a Series of lengths. `>15` make it a Series of Booleans.

```python
tweets[tweets['content'].str.len() > 15][['tweet_id']]
```

&nbsp;

## Summary

For bracket selection of Pandas DataFrame, the conditions in bracket involve various operators or functions of Pandas Series ([Common Pandas Series Operators and Functions](https://liyangsong.github.io/posts/common_pandas_series_operators_and_functions/), most of which are not only different from Python operators and functions, but a bit different from Pandas DataFrame operators and functions.

Also, always notice the `inplace` argument of functions that manipulate the DataFrame. Usually the question requires a changed DataFrame as return result. 
