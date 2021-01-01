---
layout: post
title: "Pointer"
subtitle: "풀었던 문제 모음"
date: 2020-12-30 12:25:00
author: "Dev X"
header-img: "img/post-bg/post-bg-til.jpg"
catalog: true
published: true
tags:
    - Algorithms
---

## Count unique value
문제: 배열에서 unique한 요소의 갯수를 반환해라

#### 내 풀이
멀티플 포인터를 적용해 풀어보았다.

```ts
function countUniqueValues(arr):number{
  let startPointer = 0;
  let endPointer = 0;
  let count = 0;

  while(startPointer<arr.length){
    if(arr[startPointer]===arr[endPointer]){
      // 두 포인터가 가리키는 값이 같다면 endPointer를 증가시킨다
      endPointer++;
    }else{
      //두 포인터가 가리키는 값이 같지 않다면 startPointer를 endPointer와 같게 설정한다
      count++;
      startPointer = endPointer;
    }
  }
  return count;
}
```
시간 복잡도 O(n)

### 다른 풀이 참조
while대신 for문 사용하기

```ts
function countUniqueValues(arr):number{
  let i = 0;
  for(let j = 1; j<arr.length; j++){
    if(arr[i] !== arr[j]){
      i++;
      arr[i] = arr[j];
    }
  }
  return i;
}
```
count 없이 i의 index를 이용하는 방식
시간 복잡도: O(n)


## averagePair
배열의 두 요소 합의 평균이 avarage와 일치하는가?
배열: Integer를 요소로 갖고 정렬된 상태

```js
function averagePair(arr,average){
  const addedResult = average * 2
  if(!Number.isInteger(addedResult)) return false;

  for(let i = 0, j = arr.length -1; i<j;){
    const addedPair = arr[i]+arr[j];
    if(addedPair===addedResult) return true;
    if(addedPair>addedResult){
      j--
    }else{
      i++
    }
  }
  return false;
}
```
while말고 for문을 이용해 풀어보았다.
조건에 따라 i와 j값을 증감시켜야해서 for문을 변형시켜 사용했는데
이러면 while과 뭐가 다른가? 싶긴 함..ㅠ.ㅠ

시간 복잡도: O(n)
공간 복잡도: O(n)