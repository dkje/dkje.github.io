---
layout: post
title: "Sliding Window"
subtitle: "문제 풀이 모음"
date: 2020-12-30 12:25:00
author: "Dev X"
header-img: "img/post-bg/post-bg-til.jpg"
catalog: true
published: true
tags:
  - Algorithms
---

### Grumpy Bookstore Owner

[🎁링크](https://leetcode.com/problems/grumpy-bookstore-owner/)

문제: 심술궂은 서점 주인은 X분 동안 친절해질 수 있는 스킬이 있다. 주인은 스킬을 단 1회 사용 할 수 있다(X분을 쪼개서 사용 할 수 없음) 손님들이 가장 만족했을 때의 만족도 총 합을 구하여라.

Input1 : 손님 수의 배열(index 하나당 1분이며, 손님은 1분만 가게에 머문다)
Input2 : 가게 주인이 심술궂은지의 여부(0이면 false, 1이면 true). 가게 주인이 심술궂을 시간에 방문한 손님은 불만족스럽게 떠난다.
Input3 : 가게 주인이 오늘 사용 할 수 있는 스킬의 최장 시간.

Output : 오늘 손님들의 최대 만족도

#### 내 풀이

```ts
function maxSatisfied(
  customers: number[],
  grumpy: number[],
  X: number
): number {
  if (!customers.length) return 0;
  const defaultSatisfied = customers.reduce(
    (acc, cur, i) => (grumpy[i] ? acc : acc + cur),
    0
  );

  let start = 0,
    end = 0,
    temp = 0,
    max = 0;

  while (end < customers.length) {
    if (X === 0) {
      if (grumpy[start]) temp = temp - customers[start];
      start++;
      X++;
    } else {
      if (grumpy[end]) temp = temp + customers[end];
      if (temp > max) max = temp;
      end++;
      X--;
    }
  }

  return defaultSatisfied + max;
}
```

Runtime: 92 ms, faster than 71.43% of TypeScript online submissions for Grumpy Bookstore Owner.
Memory Usage: 43.3 MB, less than 57.14% of TypeScript online submissions for Grumpy Bookstore Owner.

시간복잡도: O(n)
공간복잡도: O(1)

처음 문제를 잘못 이해하는 바람에 삽질을 너무 많이 했다😂
스킬을 연달아 사용 할 수 있는 시간인 X를 subArray의 length로 생각해서,
스킬 효율이 가장 좋은 subArray를 구하는 방식(슬라이딩 윈도우)로 풀었다.

## 중복되지 않는 문자열로 이루어진 가장 긴 배열의 길이

Input: 무작위 길이의 문자열
Output: 중복된 문자가 없는 부분 문자열의 가장 긴 길이

```js
function findLongestSubstring(str) {
  const charCount = {};
  let start = 0,
    end = 0,
    max = 0;

  while (end < str.length) {
    const endChar = str[end];
    if (!charCount[endChar]) {
      charCount[endChar] = 1;
      end++;
      const curLength = end - start;
      if (curLength > max) max = curLength;
    } else {
      const startChar = str[start];
      delete charCount[startChar];
      start++;
    }
  }

  return max;
}
```

시간복잡도: O(n)
공간복잡도: O(1)

#### 다른 사람의 풀이

```js
function findLongestSubstring(str) {
  let longest = 0;
  let seen = {};
  let start = 0;

  for (let i = 0; i < str.length; i++) {
    let char = str[i];
    if (seen[char]) {
      start = Math.max(start, seen[char]);
    }
    // index - beginning of substring + 1 (to include current in count)
    longest = Math.max(longest, i - start + 1);
    // store the index of the next char so as to not double count
    seen[char] = i + 1;
  }
  return longest;
}
```

각 문자 별로 인덱스를 갱신하며, str[i]번째에서 가장 중복이 없이, 긴 문자열을 계산하는 방식.
str의 length만큼의 시간이 걸려 위의 풀이보다 빠르다.
