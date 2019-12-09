---
title: Reverse Integer
date: 2019-12-09 14:17:23
tags: leetcode
categories:
lang:
---

# 题目
Given a 32-bit signed integer, reverse digits of an integer.

Example 1:
```
Input: 123
Output: 321
```
Example 2:
```
Input: -123
Output: -321
```
Example 3:
```
Input: 120
Output: 21
```
Note:
Assume we are dealing with an environment which could only store integers within the 32-bit signed integer range: [−231,  231 − 1]. For the purpose of this problem, assume that your function returns 0 when the reversed integer overflows.

# 解答
## Rust
```rust
fn main() {
    let r1 = reverse(-123);
    let r2 = reverse_2(-123);
    print!("r1 {}, r2 {}\n", r1, r2);
}

pub fn reverse(x: i32) -> i32 {
    let mut next = x as i64;
    let mut result: i64 = 0;
    let mut digit: i64;
    let base: i64 = 2;
    let upper_bound: i64 = base.pow(31) - 1;
    let lower_bound: i64 = -base.pow(31);

    while next != 0 {
        digit = next % 10;
        result = result * 10 + digit;
        next = next / 10;
    }

    if result > upper_bound || result < lower_bound {
        return 0;
    }

    result as i32
}

pub fn reverse_2(x: i32) -> i32 {
    x.signum() * x  // 保留符号
        .abs()      // 取绝对值
        .to_string()    // 转换成String类型
        .chars()        // 把String转换成iterator
        .rev()          // 翻转一个iterator
        .collect::<String>() //把iterator转换成String类型的collection
        .parse::<i32>() // 把String转成i32类型
        .unwrap_or(0)   // 把Result类型的结果unwrap，转成功了返回Ok的内容，失败了返回0
}
```

# 笔记
上面是这个题的两种解法，第二种看上去非常简洁。
