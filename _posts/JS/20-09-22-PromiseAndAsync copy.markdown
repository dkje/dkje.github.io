---
layout: post
title: "[JS] Promise와 Async await"
subtitle: " 짠~ 사실 둘 다 Promise였습니다~ "
date: 2020-09-22 21:22:00
author: "Dev X"
header-img: "img/post-bg/post-bg-js.png"
published: true
catalog: true
tags:
  - JS
  - Promise
  - Async
---

[[JS] 비동기 실행과 Runtime](https://dkje.github.io/2020/09/20/AsyncAndEventLoop/)에서 JS의 비동기 작업이 Runtime에서 어떻게 실행되는 지에 대해 정리했습니다. JS의 비동기 작업에선 Promise 객체를 주로 사용합니다. Promise와 함께 언급되는 Async 또한 Promise의 Syntax Sugar이기 때문에, 비동기 작업을 능숙하게 사용하기 위해서는 Promise를 이해해야 합니다.

## 🤝Promise란

callStack에서 동기적으로 작업을 진행 중일때, 외부 Thread(API)에서 실행된 비동기 작업이 **'아직 진행 중인지'**, **'진행이 잘 완료되었는지'**, **'작업에 오류가 있었는지'**에 대한 정보가 필요할 때가 있습니다. Promise는 <u>작업의 진행 여부</u>와 <u>진행 완료된 값</u>에 대한 속성을 갖고 있는 유용한 객체입니다.

## ✅Promise의 속성

![promise의 value와 status의 관계](https://i.ibb.co/9Gwk5vT/2.png)

```
Promise: {
    PromiseStatus: pending || resolved || rejected ,
    PromiseValue: returnedByAsyncFunction
}
```

Promise는 PromiseStatus와 PromiseValue라는 속성 값을 갖고 있습니다. status와 value는 <u>직접적으로 접근이 불가능<u>하며 Promise의 메서드를 통해서만 접근 할 수 있습니다.

- **PromiseStatus**: Promise의 실행 상태를 값으로 갖습니다.

  - **pending**(대기): 비동기 함수의 실행이 **이행,거부 처리되기 전**의 상태입니다.
  - **resolved**(이행): 비동기 함수의 실행이 정상적으로 **완료**되었습니다.
  - **rejected**(거부): 비동기 함수의 실행 중 **오류**가 발생했습니다.

- **PromiseValue**: PromiseStatus 상황에 따라 다른 값을 갖고 있습니다.
  - **pending** 상태일때: **undefiend**를 값으로 갖습니다.
  - **resolved** 상태일때: **resolve** 함수의 실행 **파라미터**를 값으로 갖습니다.
  - **rejected** 상태일때: **reject** 함수의 실행 **파라미터**, 또는 **예외처리로 던져진 값**을 갖습니다.

## 🌈Promise 생성법

Promise를 생성하는 방법은 두 가지가 있습니다.

1. **new** 연산자를 통해 인스턴스 생성하기
2. Promise의 **resolve**, **reject** 메서드 사용하기  
   (1번에서 callback 함수의 인자로 받는 resolve,reject와 다른 메서드입니다)

### new 연산자로 생성

```js
new Promise(funcToExecuteAsync);

const funcToExecuteAsync = function (resolve, reject) {
  let something = doSomething();
  if (something) {
    resolve(something);
  } else {
    reject("Error caused by blahblah");
  }
};
```

Promise의 Constructor는 함수를 인자로 받아 <u>비동기적으로 실행</u>합니다. 비동기 실행 함수는 **resolve**와 **reject** 함수를 인자로 받습니다.

- **resolve()**: PromiseStatus를 resolved로 변경하고, 인자로 받은 값을 PromiseValue로 설정합니다.
- **reject()**: PromiseStatus를 rejected로 변경하고, 인자로 받은 값을 PromiseValue로 설정합니다.

<u>resolve와 reject를 실행하지 않고 비동기 실행을 마치게되면</u> PromiseStatus는 **pending**, PromiseValue는 **undefiend**의 값을 갖습니다. 비동기 실행된 return 값은 생성되는 Promise에 영향을 주지 않습니다.

또, reject 함수를 사용하지 않고 PromiseStatus를 rejected로 변경하는 다른 방법이 있습니다.

```js
new Promise(() => {
  throw "reject로 상태 변경";
});
```

Promise는 코드 실행 중 **예외처리**가 발생하게 되면 PromiseStatus를 **rejected**로 변경하고 함수 실행을 중단시킵니다. 그리고 **예외처리로 던져진 값**을 PromiseValue로 갖습니다.

이렇게 생성된 Promise의 Status와 Value는 <u>Promise의 prototype 메서드로만 접근해 사용</u>할 수 있습니다.

### resolve, reject로 생성

```js
let resolvedPromise = Promise.resolve("resolved value");
let rejectedPromise = Promise.reject("rejected value");
```

resolve와 reject는 Promise **인스턴스를 생성**해, **Promise.then()**을 이행시킵니다. 이 때, then은 <u>파라미터로 전달된 값을 반환 값으로 실행</u>됩니다.

```js
Promise.resolve({
  then: function (resolve) {
    resolve("then이 이행된 값을 Value로 갖는다");
  },
});
//Promise {<resolved>: "then이 이행된 값을 Value로 갖는다"}
Promise.resolve({
  then: function (resolve) {
    throw "예외처리시 rejected로 상태 변경";
  },
});
//Promise {<rejected>: "예외처리시 rejected로 상태 변경"}
```

만약 파라미터로 전달된 객체가 **thenable**(then 메서드를 갖고 있음)하다면, resolve,reject의 파라미터로 받은 값을 반환하는 then 대신, <u>객체가 갖고 있는 then을 실행</u>시킵니다. 이 then 또한 prototype then 메서드와 같은 규칙으로 Promise의 Status와 Value를 정합니다.

## ✨Promise의 프로토타입 메서드

### .then()

**then**은 Promise의 <u>비동기 실행이 완료되고 난 뒤에 호출</u>되며 callback 함수입니다. Promise의 상태에 따라 선택적으로 호출되는 두 개의 callback 함수를 인자로 받으며, 인자로 **Value**에 접근 할 수 있는 메서드입니다. resolved 상태의 Promise의 Value에 접근할때는 반드시 then 메서드를 통해야만 합니다.

```js
new Promise((resolve, reject) => {
  resolve("비동기 실행 완료!");
}).then(whenStatusResolved, whenStatusRejected);

function whenStatusResolved(PromiseValue) {
  console.log(value, "get resolved value");
}

function whenStatusRejected(PromiseValue) {
  console.log(value, "get rejected value");
}
```

- **실행 방식**: 선행된 Promise의 비동기 작업이 완료 된 후, Status가 resolved, 또는 rejected일 때 해당하는 callback 함수를 실행합니다.
- **인자**: [상태가 resolved일 때 실행할 callback, [rejected일때 실행할 callback]]
- **Callback 함수의 인자값**: PromiseValue
- **반환 값**: Stauts와 Value 값을 갖는 새로운 Promise

then 메서드는 PromiseValue를 인자로 callback 함수를 <u>비동기적으로 실행</u>시킵니다. Promise는 callback 함수의 실행 내용에 따라 설정된 Promise를 반환합니다.

- **callback에 return 값이 있을 경우**

  - return **Promise 객체**: return된 **Promise 객체의 Value와 Status**를 반환할 Promise의 값으로 설정합니다.
  - return **그 외**: **return 값**을 반환할 Promise의 Value로 설정하고, 반환할 Promise의 Status를 **resolved**로 설정합니다.
    <br>
    <br>

- **callback에 return 값이 없을 경우**

  - 반환할 Promise의 Value를 **undefiend**로, Status를 **resolved**로 설정합니다.
    <br>
    <br>

- **callback에 예외 처리가 발생했을 경우**
  - 반환할 Promise의 Status를 **rejected**로 설정하고, **예외 처리로 던져진 값**을 Value로 갖습니다.

### .catch()

```js
new Promise((resolve,reject)=>{
  reject('에러 발생')
}).catch(err){
  console.log(err)
}
```

- **실행 방식**: 선행된 Promise의 비동기 작업이 완료 된 후 Promise의 상태가 rejected라면 callback 함수를 실행합니다.
- **인자**: **reject의 파라미터로 전달된 값** 또는 **예외처리로 던져진 값**
- **반환 값**: 새로운 Promise 객체

catch는 Promise의 상태가 reject일때 비동기적으로 실행되는 메서드입니다. Promise의 Value 값이 파라미터로 실행되며, 실행된 내용에 따라 새로 생성된 Promise의 <u>Status와 Value가 설정</u>됩니다.

- **return 값이 있을 경우**
  - **return 값이 Promise일 경우**: return한 Promise의 Value와 Status를 새로 생성한 Promise의 값으로 받습니다.
  - **그 외**: 새로 생성한 Promise의 Status는 resolved로 Value는 return 값으로 설정합니다.
    <br>
    <br>
- **return 값이 없을 경우**

  - 새로 생성한 Promise의 Status는 resolved로 Value는 undefiend로 설정합니다.
    <br>
    <br>

- **예외 처리가 발생한 경우**
  - 새로 생성한 Promise의 Status는 rejected로 Value는 throw된 값으로 설정합니다.

### .finally()

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
}.finally(()=>{
  doDishWashing()
})
```

- **실행 방식**: 선행된 Promise의 비동기 작업이 완료 된 후 callback 함수를 실행합니다.
- **인자**: 반드시 마지막에 실행 될 callback 함수
- **callback 함수의 인자**: 없음
- **반환 값**: 새로운 Promise 객체

finally는 Promise의 이행 후 <u>반드시 실행</u>해야할 작업이 있을 때 사용하는 메서드입니다. Promise의 Status 값과 관련없이 실행되며, then과 catch에서 발생 할 수 있는 코드 중복을 줄이는 목적으로 사용됩니다.

### then,catch와 finally의 차이점

```js
new Promise((resolve, reject) => {
  resolve("resolve value");
})
  .then((value) => {
    return `${value} and then method executed`;
  })
  .finally(() => "finally method executed");

//Promise {<resolved>: "resolve value and then method executed"}
```

- **인자 값**
  - then, catch: 메서드를 가진 Promise 인스턴스의 Value 값
  - finally: 받지 않는다.
    <br><br>
- **반환할 Promise의 Status 값**
  - then, catch: 예외가 발생하지 않는다면 resolve 시킨다.
  - finally: 예외가 발생하지 않는다면 변경하지 않는다.
    <br><br>
- **반환할 Promise의 Value 값**
  - then, catch: callback 함수의 return 값에 영향을 갖는다.
  - finally: return 값이 영향을 주지 않는다.

finally가 반환될 Promise에 영향을 주는 경우는 **예외 처리가 발생**했을때 뿐입니다.

```js
new Promise((resolve) => {
  resolve("완료");
}).finally(() => {
  throw "거절";
});
// Promise {<rejected>: "거절"}
```

finally의 구문에서 예외처리가 발생 했을 경우 Promise는 <u>Status를 rejected로 변경</u>하고 <u>예외처리로 던져진 값을 Value</u>로 갖습니다.

## 🔗Promise Chaining

![promise chaining의 그림 예시](https://i.ibb.co/k5vjQvR/image.png)

```js
new Promise((resolve, reject) => {
  let something = doSomething();
  if (something) {
    resolve(something);
  } else {
    reject("The Reason why error occurred");
  }
})
  .then((data) => {
    let somethingElse = doSomethingElse(data);
    return somethingElse;
  })
  .catch((e) => {
    doSomeFix(e);
    return "Error Fixed";
  })
  .finally(() => {
    doSomeThingEssential();
  });
```

**then, catch, finally**가 <u>새로 생성된 Promise 객체를 return하는 특징</u>을 이용하면 메서드 체인 방식으로 사용할 수 있습니다. 이런 사용 법을 **Promise Chaining**이라고 합니다. Promise의 메서드는 비동기적으로 실행되기 때문에 <u>비동기 작업 후 호출해야하는 Callback이 있을 때</u> 유용하게 사용 할 수 있는 방법 입니다.

## Async Function

![힝 속았지?](https://i.ibb.co/58SQp72/thumb-asmc-await-promise-chaining-i-think-async-await-was-made-for-61245442.png)

Async 함수는 Promise Chaining을 우리에게 익숙한 코드 정렬로 사용 할 수 있도록 만들어진 문법입니다. 여전히 Promise를 사용하는 것이지만 직관적으로 코드를 이해하는데 큰 도움이 됩니다.

```js
async function asyncFunc1() {
  return "returned by asyncFunc";
} //Promise {<resolved>: "returned by asyncFunc"}
async function asyncFunc2() {
  return new Promise(() => {});
} //Promise {<pending>}
```

함수의 선언 앞에 **async** 키워드를 붙이면 그 함수는 **Promise**로 결과 값을 반환하는 **비동기 실행 함수**가 됩니다.

- **반환 값**: 반드시 **Promise** 객체를 반환한다.
  - return 값이 **Promise 객체** 일 때: return 된 것과 동일한 Status와 Value를 갖는 Promise 객체
  - return 값이 **그 외**일 때: return 값을 Value로 받고, resolved 된 Promise 객체

### await

```js
async function asyncfunc() {
  console.log("async 실행");
  await new Promise((resolve) => {
    setTimeout(() => {
      console.log("promise resolve됨");
      resolve();
    }, 2000);
  });
  console.log("대기 끝");
}
////////console////////
//async 실행
// ~ 2 초후 ~
//promise resolve됨
//대기 끝
//////////////////////
```

**await**는 비동기 작업 실행의 결과 값이 필요한 callback 함수가 있을 때, 유용한 문법입니다. **async**로 선언된 함수 안에서, **Promise 객체의 앞**에 붙여 사용합니다. await 키워드가 붙은 Promise는 처리되기 전까지 <u>다음 줄을 실행하지 않고 대기</u> 시킵니다. **then** 메서드과 같은 목적의 문법이지만 사용법이 더 간략화 되어있습니다.

**사용 문법**

- Async 함수의 내부에서만 사용
- Promise 객체의 앞에 선언

**다음 줄이 실행되는 조건**

- Promise가 resolve 또는 reject 되었을때 다음 줄의 코드를 실행한다.
  (Promise가 Pending 상태이면 다음 줄이 실행되지 않는다)

**반환 값**

- Promise의 Value값

### await Promise의 예외 처리

```js
async function asyncFunc() {
  try {
    let promise = await new Promise((resolve, reject) => {
      console.log("작업 진행 중");
      reject("에러 발생");
      // throw '에러 발생'
    });
  } catch (err) {
    console.log("catch:", err);
  } finally {
    console.log("finally: 실행 종료");
  }
}
////////console////////
//작업 진행 중
//catch: 에러 발생
//finally: 실행 종료
///////////////////////
```

await 키워드는 then과 catch 메서드보다 직관적이지만, Promise의 Status에 접근할 수 없다는 문제점이 있습니다. async 함수에서 await Promise가 **reject**가 처리되었을때의 예외는 try... catch 문을 통해 처리됩니다. Promise의 then과 catch 메서드가 Promise의 prototype 메서드였던 것과 다르게, try... catch문은 <u>async 함수에서만 사용되는 문법이 아닙니다.</u>

#### try... catch

```js
function func() {
  try {
    doUncertainTask();
  } catch (err) {
    handlingError(err);
  } finally {
    doSomethingMustBeDone();
    return "done";
  }
}
```

try... catch는 실행 결과 값이 확실하지 않은 작업에서 **예외 처리**가 필요한 경우 사용하는 문법입니다. Promise의 then, catch 구문과 달리 return 값을 명시적으로 정할 수 있어 사용이 더 자유롭습니다.

- **try block**: 예외가 발생할 수도 있는 작업을 감쌉니다.
- **catch block**: 발생한 예외를 처리 할 작업을 감쌉니다. 인자로 throw된 값을 받습니다.
- **finally block**: 반드시 실행해야할 작업을 감쌉니다.

## [Posting Reference]

[Promise](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Promise)  
[JavaScript Visualized: Promises & Async/Await](https://dev.to/lydiahallie/javascript-visualized-promises-async-await-5gke)  
[프라미스](https://ko.javascript.info/promise-basics)  
[프라미스 체이닝](https://ko.javascript.info/promise-chaining)  
[async function](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Statements/async_function)  
[async와 await](https://ko.javascript.info/async-await)  
[try...catch](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Statements/try...catch)
