---
layout: post
title: "Frequency"
subtitle: "풀었던 문제 모음"
date: 2020-12-30 12:25:00
author: "Dev X"
header-img: "img/post-bg/post-bg-til.jpg"
catalog: true
published: true
tags:
    - Algorithms
---

## Valid Anagram

[LeetCode 242](https://leetcode.com/problems/valid-anagram/)

#### 내 풀이
Count 패턴을 적용해 풀어보려고 했다.
풀이 방향은 잘 잡았지만 중간에 count[]++ 과 ++count[]의 차이를 잠시 헷갈려
그 부분을 디버깅 하는데 시간이 걸렸다.

```ts
function isAnagram(s: string, t: string): boolean {
  if(s.length!==t.length) return false;
  const strLength = s.length;

  const sCount:Record<string,number> = {};
  const tCount:Record<string,number> = {};
  for(let i=0; i<strLength; i++){
    const currentSChar = s[i];
    sCount[currentSChar] = ++sCount[currentSChar] || 1
    const currentTChar = t[i];
    tCount[currentTChar] = ++tCount[currentTChar] || 1
  }

  for(let char in sCount){
    if(sCount[char] !== tCount[char]) return false;
  }

  return true
};
```
Runtime: 96 ms, faster than 74.78% of TypeScript online submissions for Valid Anagram.
Memory Usage: 41.7 MB, less than 50.43% of TypeScript online submissions for Valid Anagram.

for문을 가능한 적게 사용해보려고 했지만 해싱 하는데 O(n)의 시간이 걸리고 만다.
다른 방식으로 풀려면 문자열을 배열로 변환한 뒤 sort 시켜 비교하는 방식도 있을 것이다.

### 다른 풀이 참조
공간 복잡도 줄이기

```ts
function isAnagram(first, second):boolean {
  if (first.length !== second.length) return false;

  const lookup:Record<string, number> = {};
  for (let i = 0; i < first.length; i++) {
    let letter = first[i];
    lookup[letter] ? lookup[letter] += 1 : lookup[letter] = 1;
  }

  for (let i = 0; i < second.length; i++) {
    let letter = second[i];
    if (!lookup[letter]) {
      return false;
    } 
    lookup[letter] -= 1;
  }

  return true;
}
```
Runtime: 88 ms, faster than 93.91% of TypeScript online submissions for Valid Anagram.
Memory Usage: 40.5 MB, less than 96.52% of TypeScript online submissions for Valid Anagram.

first를 기준으로 count를 높이고 second를 기준으로 count를 줄이는 방식
object를 하나 덜 사용 할 수 있다.