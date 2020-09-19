---
layout: post
title: "[JS] Closure와 Execution Context"
subtitle: "  "
date: 2020-09-18 17:16:00
author: "Dev X"
header-img: "img/post-bg/post-bg-js.png"
published: true
catalog: true
tags:
    - JS
    - closure
    - context
    - scope
---

[[JS]Execution Context와 Call Stack](https://dkje.github.io/2020/08/30/ExecutionContext/)에서 이어지는 포스팅입니다. Context와 관련된 자세한 내용은 이전 글에 정리했기 때문에 이 글에서는 closure와 관련된 내용들을 중점적으로 정리했습니다.

## 📸Closure란?

[지난 글](https://dkje.github.io/2020/08/30/ExecutionContext/)에서 Execution Context를 다루며 scope에 대한 내용도 간략하게 정리를 했습니다. 짧게 정리하자면, scope는 lexical environment에서 정의한 객체의 유효 범위라는 것이죠. 하지만 JS에는 특이한 scope가 하나 더 있습니다.

```js
function makeClosureFunc(hi) {
    const name = "peter";
    return function () {
        console.log(`${hi} ${name}~!`);
    };
}

const sayHiToPeter = makeClosureFunc("Good morning!");
sayHiToPeter(); //Good morning! peter~!
```

위의 코드를 예시로 들겠습니다.

-   makeClosureFunc : 익명 함수를 return 합니다.
-   반환된 함수: makeClosureFunc의 scope에 있는 name과 hi 변수를 사용하고 있습니다.

CallStack의 변화는 이렇습니다.

1.  makeClosureFunc의 Functional Execution Context가 추가된다
2.  makeClosureFunc의 Functional Execution Context가 삭제된다
3.  sayHiToPeter의 Functional Execution Context가 추가된다
4.  sayHiToPeter의 Functional Execution Context가 삭제된다

sayHiToPeter은 makeClosureFunc의 block 밖에 선언되어있는데도 Reference error가 발생하지 않았습니다. 즉, makeClosureFunc가 실행 종료되어 Context가 CallStack에서 삭제 되었어도, return된 함수는 makeClosureFunc의 lexical environment를 참조할 수 있다는 뜻이죠.

이처럼 Outer Reference의 Execution Context가 제거되어도 <u>자신이 정의된 환경의 정보(Outer Reference)를 갖고있는 함수</u>가 있습니다. 그 **함수**와 **Lexical Environment**를 묶어 **Closure**라고 부릅니다.

## Closure의 구현 방식

다시 한 번 Execution Context의 정의를 해보겠습니다. **Execution Context**는 함수가 실행되는 **환경 정보**를 갖고 있는 객체입니다. 함수가 실행 될 때 CallStack에 추가되며 실행이 종료되면 CallStack에서 종료되죠. 하지만 <u>CallStack에서 제거된다는 것이 메모리에서 삭제됨을 뜻하는 것은 아닙니다.</u>

### 🧺Garbage Collection

JS의 메모리는 3가지 생명주기를 갖습니다.

1. 필요하면 할당한다.
2. 사용한다
3. 필요없어지면 해제한다.

저수준 언어에서는 명시적으로 메모리를 할당 해제하지만 JS에선 수동으로 메모리를 관리하는 대신 **가비지 컬렉션**이 알아서 메모리를 관리해줍니다. 메모리에 할당된 값이 더는 필요하지 않다고 판단될때 메모리를 해제시키는 과정을 **가비지 컬렉션**이라고 부르며 이 역할을 **가비지 컬렉터**가 맡고 있습니다.

가비지 컬렉터가 '필요없다'라고 판단하는 기준은 더 이상 <u>'객체에 닿을 수 없을 때'</u>를 말합니다. 닿는다는 roots(전역 변수)를 기준으로 참조, 또는 참조의 참조의... 참조가 되는 객체들입니다. 이 알고리즘을 mark and sweep이라고 부르는데 가비지 컬렉터는 'root에서 닿을 수 있는' 객체들의 reachable을 true로 표시하고, false인 객체들은 메모리에서 해제시킵니다.

다시 코드를 예시로 보겠습니다.

```js
function makeClosureFunc(hi) {
    const name = "peter";
    return function () {
        console.log(`${hi} ${name}~!`);
    };
}

const sayHiToPeter = makeClosureFunc("Good morning!");
sayHiToPeter(); //Good morning! peter~!
```

-   makeClosureFunc가 실행되며 Execution Context가 CallStack에 추가됩니다.

```
makeClosureFunc's Lexical Environment:{
    Environment Record:{
        hi: 'Good morning!',
        name: 'peter'
    },
    Outer Reference:{
        Global Lexical Environment
    }
}
```

-   return 값으로 반환되는 Closure 함수도 선언부를 기준으로 Lexical Environment가 정의됩니다.

```
Closure's Lexical Environment: {
    Environment Record:{
        hi: 'Good morning!',
        name: 'peter'
    },
    Outer Reference:{
        Global Lexical Environment:{
            makeClosureFunc's Lexical Environment
        }
    }
}
```

-   makeClosureFunc의 함수 실행이 종료되며 makeClosureFunc의 Execution Context가 CallStack에서 제거됩니다.
-   **가비지 컬렉터**가 <u>makeClosureFunc의 Lexical Environment</u>를 메모리에서 지워도 될 지 reachable을 확인합니다.
-   전역 변수인 **sayHiToPeter**가 Closure 함수를 참조하고 있고, Closure 함수의 Outer Reference가 makeClosureFunc의 Lexical Environment를 참조하고 있기 때문에 <u>makeClosureFunc의 Lexical Environment는 root에서 닿을 수 있는 상태</u>입니다.
-   makeClosureFunc의 Lexical Environment는 메모리에서 제거되지 않습니다.

![그림으로 표현한 닿을 수 있는 객체의 표시](https://i.ibb.co/prmz48K/image.png)

즉, Closure 함수를 전역 객체가 참조하고 있기 때문에 Closure의 Outer Reference도 메모리에서 삭제되지 않고 계속 유지되는 것이죠.

## Closure는 어떻게 써야하나?

Closure의 가장 대표적 사용 목적은 private한 data를 만들어야 할 때입니다. JS는 객체지향언어이지만 캡슐화를 위한 private을 지원하지는 않습니다. 외부에 노출되지 않는 객체의 이름에 '\_'를 붙여 사용하는 암묵적 네이밍 약속 등을 사용하고는 있지만, 약속일 뿐 기능적 효과는 없습니다. 만약 인터페이스를 만들어야 할 때 외부에 노출되어선 안되는 data가 있다면 Closure를 통해 private 객체를 구현 할 수 있습니다.

```js
const makeClosure = function () {
    const message = "private data";
    const privateFunc = function () {
        console.log(`${message} can only be accessed through closure`);
    };

    return {
        runPrivateFunc: privateFunc,
    };
};

const closure = makeClosure();
closure.runPrivateFunc();
```

외부에서 closure의 외부 환경 변수에 접근하기 위해서는 반드시 closure의 접근 권한을 통해야만 합니다. message는 외부 노출되지 않았기 때문에 직접적으로 읽기, 수정이 불가능합니다.

<span style="color:grey">
es2019에 private class를 선언 할 수 있는 문법이 추가되었습니다. 
[Private class fields](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Classes/Private_class_fields)
</span>

## 메모리 누수 문제

가비지 컬렉터는 알아서 메모리 관리를 해준다는 장점이 있지만, 반대로 수동으로 메모리 해제를 할 수 없기 때문에 발생하는 메모리 누수 문제가 있습니다. Closure는 Mark And Sweep 알고리즘을 이용해 실행이 끝난 함수의 Lexical Environment를 메모리에서 해제하지 않고 유지합니다. 하지만 Closure의 필요성을 마친 뒤에도 '필요없음'을 가비지 컬렉터에게 알리지 않는다면 메모리 누수가 발생하게 됩니다.

Closure가 이제 필요없음을 전달하는 방법은 간단합니다. Closure를 담고있는 객체를 다른 값으로 초기화 시키면 더 이상 root에서 참조되지 않는 Closure를 가비지 컬렉터가 메모리 해제하게 됩니다.

```js
function makeClosureFunc(hi) {
    const name = "peter";
    return function () {
        console.log(`${hi} ${name}~!`);
    };
}

const sayHiToPeter = makeClosureFunc("Good morning!");
sayHiToPeter(); //Good morning! peter~!

// ~ 함수의 필요성이 완료됨 ~

sayHiToPeter = null;
// 가비지 컬렉터가 이전 할당 값인 Closure의 reachable이 false 된 것을 확인하고 메모리에서 해제 함
```

또는 Closure의 메모리 누수 문제 때문에 Closure의 사용을 자제해야한다는 의견도 있습니다. 재 사용성이 없는 객체라면 즉시 실행함수를 통해서도 private data를 구현 할 수 있습니다.

```js
(function makePrivateFunc() {
    const message = "private data";
    const privateFunc = function () {
        console.log(`${message} can also implemented through the IIFE`);
    };
    privateFunc();
})();
```

## [Posting Reference]

[자바스크립트의 스코프와 클로저](https://meetup.toast.com/posts/86)  
[Execution Context, Closure](https://velog.io/@koseungbin/Execution-Context-Closure)  
[[JavaScript] 실행 영역(Execution Context), Scope, Closure](https://ggobugi.tistory.com/248)  
[The Ultimate Guide to Hoisting, Scopes, and Closures in JavaScript](https://ui.dev/ultimate-guide-to-execution-contexts-hoisting-scopes-and-closures-in-javascript/)  
[자바스크립트의 메모리관리](https://developer.mozilla.org/ko/docs/Web/JavaScript/Memory_Management)  
[가비지 컬렉션](https://ko.javascript.info/garbage-collection)  
[자바스크립트는 어떻게 작동하는가: 메모리 관리 + 4가지 흔한 메모리 누수 대처법](https://engineering.huiseoul.com/%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8%EB%8A%94-%EC%96%B4%EB%96%BB%EA%B2%8C-%EC%9E%91%EB%8F%99%ED%95%98%EB%8A%94%EA%B0%80-%EB%A9%94%EB%AA%A8%EB%A6%AC-%EA%B4%80%EB%A6%AC-4%EA%B0%80%EC%A7%80-%ED%9D%94%ED%95%9C-%EB%A9%94%EB%AA%A8%EB%A6%AC-%EB%88%84%EC%88%98-%EB%8C%80%EC%B2%98%EB%B2%95-5b0d217d788d)  
[How can closures cause memory leak and how to prevent it?](https://www.tutorialspoint.com/how-can-closures-cause-memory-leak-and-how-to-prevent-it)
