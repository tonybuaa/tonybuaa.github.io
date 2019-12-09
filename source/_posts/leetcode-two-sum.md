---
title: Two Sum
date: 2019-12-06 15:40:50
tags: leetcode
categories:
---

# 题目
Given an array of integers, return indices of the two numbers such that they add up to a specific target.

You may assume that each input would have exactly one solution, and you may not use the same element twice.

Example:
```
Given nums = [2, 7, 11, 15], target = 9,

Because nums[0] + nums[1] = 2 + 7 = 9,
return [0, 1].
```

# 解答
## Rust
```rust
use std::collections::HashMap;

fn main() {
    let nums = vec![2,7,11,15];
    let target = 9;
    let r = two_sum(nums, target);

    print!("r {:?}\n", r);
}

fn two_sum(nums: Vec<i32>, target: i32) -> Vec<i32> {
    let mut seen: HashMap<i32, i32> = HashMap::new();
    for (i, num) in nums.iter().enumerate() {
        if seen.contains_key(num) {
            return vec![seen[num], i as i32];
        } else {
            seen.insert(target - num, i as i32);
        }
    }
    vec![]
}
```

# 笔记
1. `iter().enumerate()`返回的index类型是`usize`，而我们要返回的Vec是`i32`类型的，所以在用`vec!`构建Vec的时候要用`as`转一下。
2. insert到HashMap后，所有权也被转移到HashMap。
3. `seen[num]`这种用法实际上是*seen.index(num)的语法糖，对于immutable的值，这实际上是一种Copy行为。