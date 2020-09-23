---
layout: post
title: "[JS] Promise와 Async await"
subtitle: " 짠~ 사실 둘 다 Promise였습니다~ "
date: 2020-09-22 21:22:00
author: "Dev X"
header-img: "img/post-bg/post-bg-js.png"
published: false
catalog: true
tags:
  - JS
  - Promise
  - Async
---

[[JS] 비동기 실행과 Runtime](https://dkje.github.io/2020/09/20/AsyncAndEventLoop/)에서 JS의 비동기 작업이 Runtime에서 어떻게 실행되는 지에 대해 정리했습니다. JS의 비동기 작업에선 Promise 객체를 주로 사용합니다. Promise와 함께 언급되는 Async 또한 Promise의 Syntax Sugar이기 때문에, 비동기 작업을 능숙하게 사용하기 위해서는 Promise를 이해해야 합니다.


## 🤝Promise란

callStack에서 동기적으로 작업을 진행 중일때, 외부 Thread(API)에서 실행된 비동기 작업이 **'아직 진행 중인지'**, **'진행이 잘 완료되었는지'**, **'작업에 오류가 있었는지'**에 대한 정보가 필요 할 때가 있습니다. Promise는 <u>작업의 진행 여부</u>와 <u>진행 완료된 값</u>에 대한 속성을 갖고 있는 유용한 객체입니다.

## Promise의 속성

![promise의 value와 status의 관계](https://i.ibb.co/gSN1ypF/2020-09-23-113445.png)

```
Promise: {
    PromiseStatus: pending || resolved || rejected ,
    PromiseValue: returnedByAsyncFunction
}
```

Promise는 PromiseStatus와 PromiseValue라는 속성 값을 갖고 있습니다. status와 value는 직접적으로 접근이 불가능하며 Promise의 메서드를 통해서 사용 할 수 있습니다.

- **PromiseStatus**: Promise의 실행 상태를 값으로 갖습니다.
  - **pending**(대기): 비동기 함수의 실행이 **이행,거부 처리되기 전**의 상태입니다.
  - **resolved**(이행): 비동기 함수의 실행이 정상적으로 **완료**되었습니다.
  - **rejected**(거부): 비동기 함수의 실행 중 **오류**가 발생했습니다.

- **PromiseValue**: PromiseStatus 상황에 따라 다른 값을 갖고 있습니다.
  - **pending** 상태일때:  **undefiend**를 값으로 갖습니다. 
  - **resolved** 상태일때: **resolve** 함수의 실행 **파라미터**를 값으로 갖습니다.
  - **rejected** 상태일때: **reject** 함수의 실행 **파라미터**를 값으로 갖습니다.


## Promise 생성 법

Promise 객체를 생성하는 방법은 두 가지가 있습니다.

1. **new** 키워드로 Promise의 **인스턴스**를 생성한다.
2. Promise 객체를 반환하는 **API**를 사용한다.

```js
new Promise(funcToExecuteAsync);

const funcToExecuteAsync = function(resolve,reject){
  let something = doSomething();
  if(something){
    resolve(something);
  }else{
    reject('Error caused by blahblah');
  }
}

```

new 키워드로 Promise를 생성하는 방법은 위와 같습니다. Promise의 Constructor는 함수를 인자로 받아 <u>비동기적으로 실행</u>합니다. 생성된 Promise는 비동기 실행 함수가 완료되지 않은 동안은 value 값으로 상태이며, 

- resolve:
- reject:


## Promise의 메서드

### Promise.all()

## Promise의 프로토타입 메서드

### .then()

### .catch() & .finally

```js
new Promise((resolve,reject)=>{
  let eatDinner = eatSandwich();
  if(eatDinenr){
    resolve(eatDinner);
  }else{
    reject('I have indigestion')
  }
}).catch(e){
  goToHospital(e);
}.finally{
  doDishWashing()
}
```

1. 내가 밥하는 동안, 사와라
2. reject: ~~해서 못사왔어
3. catch: 에효. 그럼 라면이라도 끓이자
4. resolve: 여기 니가 시킨 ~~사왔어
5. then: 그럼 밥먹자!
6. finally: 설거지 함

## Promise Chaining
```js
new Promise((resolve,reject)=>{
  let something = doSomeThing();
  if(something){
    resolve(something)
  }else{
    reject('Why the error occurred')
  }
}).then((data)=>{
  let somethingAsync1 = doSomethingAsync1(data);
  return somethingAsync1
}).then((data)=>{
  let somethingAsync2 = doSomethingAsync2(data);
  return somethingAsync2
})
```

## Async await

![힝 속았지?](https://i.ibb.co/58SQp72/thumb-asmc-await-promise-chaining-i-think-async-await-was-made-for-61245442.png)


## [Posting Reference]

[Promise](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Promise)  
[JavaScript Visualized: Promises & Async/Await](https://dev.to/lydiahallie/javascript-visualized-promises-async-await-5gke)
