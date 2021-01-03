---
layout: post
title: "Recursion"
subtitle: "문제 풀이 모음"
date: 2021-01-03 12:25:00
author: "Dev X"
header-img: "img/post-bg/post-bg-til.jpg"
catalog: true
published: true
tags:
  - Algorithms
---

## String Reverse

문제: 주어진 문자열을 뒤집어 반환해라
ex:) 'awesome' => 'emosewa' // 'rithmschool' => 'loohcsmhtir'

#### 내 풀이

```js
function reverse(str) {
  if (str.length === 1) return str;
  return str.slice(-1) + reverse(str.slice(0, -1));
}
```

처음엔 substring을 써서 풀었는데 slice를 써서 풀었을 때 더 코드가 짧아지고 가독성이 좋아졌다

#### 다른 사람의 풀이 참조

```js
function reverse(str) {
  if (str.length <= 1) return str;
  return reverse(str.slice(1)) + str[0];
}
```

나는 뒷 글자를 앞으로 이동시키는 방식이었는데
반대로 앞 글자를 뒤로 이동시키는 방식이다.
순서만 바꿨을 뿐인데 코드를 읽기에 훨씬 직관적이다.

## isPalindrome

문제: 문자열의 문자가 대칭형으로 이루어졌다면 true, 아니라면 false를 반환하시오

#### 내 풀이

```js
function isPalindrome(str) {
  // 문자열 중간을 기준으로 앞 뒤를 분리한다
  let strA = str.substring(0, Math.floor(str.length / 2));
  let strB = str.substring(Math.round(str.length / 2), str.length);

  // 문자열을 뒤집기 위한 재귀 helper 함수
  const helper = (str) => {
    if (str.length === 1) return str;
    return str.slice(-1) + reverse(str.slice(0, -1));
  };

  // 뒤집은 문자열이 다른 것과 동일한지의 여부를 반환한다
  return strA === helper(strB);
}
```

#### 다른 사람의 풀이

```js
function isPalindrome(str) {
  if (str.length === 1) return true;
  if (str.length === 2) return str[0] === str[1];
  if (str[0] === str.slice(-1)) return isPalindrome(str.slice(1, -1));
  return false;
}
```

문자열을 분리하는 과정 없이 양 끝 문자를 비교하는 형식으로 재귀함수를 진행한다.

## Flatten

문제: nested된 배열을 flat하게 만들어 반환해라
ex:) [[1],[2],[3]]) // [1,2,3]

#### 내 풀이

```js
function flatten(arr) {
  let result = [];

  for (let el of arr) {
    if (!Array.isArray(el)) {
      result.push(el);
    } else {
      result = [...result, ...flatten(el)];
    }
  }

  return result;
}
```

#### 다른 사람의 풀이

```js
function flatten(oldArr) {
  var newArr = [];
  for (var i = 0; i < oldArr.length; i++) {
    if (Array.isArray(oldArr[i])) {
      newArr = newArr.concat(flatten(oldArr[i]));
    } else {
      newArr.push(oldArr[i]);
    }
  }
  return newArr;
}
```

## CapitalizeFirst

문제: string 요소를 갖는 배열이 주어진다.
각 요소들의 첫번째 글자만 대문자로 바꾼 배열을 반환해라.

#### 내 풀이

```js
function capitalizeFirst(arr) {
  // 뒤에서부터 역순으로 문자를 해체하면서 마지막 1자가 남았을때 uppercase한다
  const result = [];

  const upperWordHelper = (str) => {
    // 마지막 문자만 Uppercase해서 반환한다
    if (str.length === 1) return str.toUpperCase();
    return (
      upperWordHelper(str.substring(0, str.length - 1)) +
      str.substring(str.length - 1, str.length)
    );
  };

  for (const word of arr) {
    result.push(upperWordHelper(word));
  }

  return result;
}
```

사실 재귀로 풀지 않는게 더 효율이 좋은 문제같아서 어디에 재귀를 써야하나가 더 고민되었다.
문자열의 첫번째 글자를 대문자로 바꾸는 로직에 재귀를 끼워넣었다.

#### 다른 사람의 풀이

```js
function capitalizeFirst(array) {
  if (array.length === 1) {
    return [array[0][0].toUpperCase() + array[0].substr(1)];
  }
  const res = capitalizeFirst(array.slice(0, -1));
  const string =
    array.slice(array.length - 1)[0][0].toUpperCase() +
    array.slice(array.length - 1)[0].substr(1);
  res.push(string);
  return res;
}
```

배열을 순회하는 로직을 재귀로 처리했다

## NestedEvenSum

문제: 객체의 요소 중 짝수인 수의 합을 구하여라

#### 내 풀이

```js
function nestedEvenSum(obj) {
  let result = 0;

  for (let key in obj) {
    if (typeof obj[key] === "object") {
      result += nestedEvenSum(obj[key]);
    } else if (obj[key] % 2 === 0) {
      result += obj[key];
    }
  }

  return result;
}
```

#### 다른 사람의 풀이

```js
function nestedEvenSum(obj, sum = 0) {
  for (var key in obj) {
    if (typeof obj[key] === "object") {
      sum += nestedEvenSum(obj[key]);
    } else if (typeof obj[key] === "number" && obj[key] % 2 === 0) {
      sum += obj[key];
    }
  }
  return sum;
}
```

## CapitalizeWords

문제: 문자열로 이루어진 배열의 모든 요소를 대문자로 바꿔라

#### 내 풀이

```js
function capitalizeWords(arr) {
  const result = [];

  const upperHelper = (str) => {
    if (str.length === 1) return str.toUpperCase();
    return str[0].toUpperCase() + upperHelper(str.substring(1, str.length));
  };

  for (let word of arr) {
    result.push(upperHelper(word));
  }

  return result;
}
```

#### 다른 사람의 풀이

```js
function capitalizeWords(array) {
  if (array.length === 1) {
    return [array[0].toUpperCase()];
  }
  let res = capitalizeWords(array.slice(0, -1));
  res.push(array.slice(array.length - 1)[0].toUpperCase());
  return res;
}
```

## StringfyNumbers

문제: 객체 요소 중 number 타입을 string으로 변환한 복사본을 반환하라.

```js
const stringifyNumbers = (obj) => {
  const copyObj = Object.assign({}, obj);
  const stringfyHelper = (parents) => {
    for (let key in parents) {
      if (typeof parents[key] === "number") {
        parents[key] = String(parents[key]);
      } else if (
        typeof parents[key] === "object" &&
        !Array.isArray(parents[key])
      ) {
        stringfyHelper(parents[key]);
      }
    }
  };
  stringfyHelper(copyObj);
  return copyObj;
};
```

#### 다른 사람의 풀이

```js
function collectStrings(obj) {
  var stringsArr = [];

  function gatherStrings(o) {
    for (var key in o) {
      if (typeof o[key] === "string") {
        stringsArr.push(o[key]);
      } else if (typeof o[key] === "object") {
        return gatherStrings(o[key]);
      }
    }
  }
  gatherStrings(obj);

  return stringsArr;
}
```

## CollectStrings

문제: 객체 요소 중 문자열만을 모은 배열을 반환해라

```js
const collectStrings = (obj) => {
  const result = [];

  const collectHelper = (parents) => {
    for (let key in parents) {
      if (typeof parents[key] === "string") {
        result.push(parents[key]);
      } else if (
        typeof parents[key] === "object" &&
        !Array.isArray(parents[key])
      ) {
        collectHelper(parents[key]);
      }
    }
  };

  collectHelper(obj);

  return result;
};
```

#### 다른 사람의 풀이

```js
function collectStrings(obj) {
  var stringsArr = [];
  for (var key in obj) {
    if (typeof obj[key] === "string") {
      stringsArr.push(obj[key]);
    } else if (typeof obj[key] === "object") {
      stringsArr = stringsArr.concat(collectStrings(obj[key]));
    }
  }

  return stringsArr;
}
```
