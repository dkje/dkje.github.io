---
layout: post
title: "[JS]Execution Context와 Call Stack"
subtitle:
date: 2020-08-30 23:21:00
author: "Dev X"
header-img: "img/post-bg/post-bg-js.png"
published: true
catalog: true
tags:
    - js
    - Execution Context
    - Call Stack
    - hoisting
    - scope chain
    - Lexical nesting
---

**ECMA-262 6th를 기준으로 작성된 글입니다**

**Execution Context**는 scope와 hoisting, this, closure등을 관통하는 가장 근본적인 개념입니다. 자바스크립트 코드의 실행 흐름과 코드에 대한 정보가 Execution Context로 관리되기 때문에 Execution Context를 이해하지 못하면 부수적인 개념들에 대해서도 완벽하게 이해 할 수 없습니다.

이 글은 Execution Context의 정의와 자바스크립트 엔진이 Execution Context를 관리하는 흐름에 대해 공부하며 정리했습니다

## Execution Context(실행 컨텍스트)란?

자바스크립트 엔진이 코드를 실행하기 위해선 <u>코드에 대한 정보</u>들이 필요합니다. 코드에 선언된 변수와 함수, 스코프, this, arguments 등을 묶어, <u>코드가 실행되는 위치를 설명한다는 뜻의</u> **Execution Context**라고 부릅니다. 자바스크립트 엔진은 Execution Context를 객체로 관리하며 코드를 Execution Context 내에서 실행합니다.

## Execution Context의 종류

실행 컨텍스트의 종류는 아래의 세 가지로 나누어집니다.

**1. Global Execution Context**  
코드를 실행하며 **단 한 개만** 정의되는 전역 Context입니다. global object를 생성하며 this 값에 global object를 참조합니다. 전역 실행 컨텍스트는 Call Stack에 가장 먼저 추가되며 앱이 종료 될 때 삭제됩니다.

**2. Functional Execution Context**  
함수가 **실행** 될 때 마다 정의되는 Context입니다. 전역 실행 컨텍스트가 단 한 번만 정의되는 것과 달리, 함수 실행 컨텍스트는 매 실행시마다 정의되며 함수 실행이 종료(return)되면 Call Stack에서 제거됩니다.

**3. Eval Context**  
[eval](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/eval) 함수로 실행한 코드의 Context입니다. 보안상 취약한 점이 있어 비권장 함수이기 때문에 이 글에서는 다루지 않습니다.

## Execution Context의 관리: CallStack

js 엔진은 생성된 Context를 관리하는 목적의 **Call Stack**(호출스택)을 갖고 있습니다. Call Stack은 LIFO(Last In First Out)구조의 단일 스레드 형식이기 때문에 단 하나만 존재합니다. (js가 동기 언어인 이유입니다) js 엔진은 전역 범위의 코드를 실행하며 Global Execution Context를 생성해 stack에 push합니다. 그리고 함수가 실행 또는 종료 될 때마다 Global Execution Context의 위로 Functional Execution Context stack을 **push**(추가), **pop**(제거)합니다.

Call Stack은 최대 stack 사이즈가 정해져있습니다. Call Stack에 쌓인 Context Stack이 최대치를 넘게 될 경우 'RangeError: Maximum call stack size exceeded'라는 에러가 발생합니다. 이 에러는 **Stack Overflow**라고 부르기도 합니다.

<span style="color:grey">자료구조 stack에 대해 더 알고 싶다면 [이 링크](https://medium.com/@jinseok.choi/%EC%8A%A4%ED%83%9D-stack-48a22e52268b)를 참고해주세요</span>

![callStack의 예시 이미지](https://i.ibb.co/zZ9zpDC/Context-Stack.gif)

1. 코드의 전역 범위가 실행되며 **Global Execution Context**를 push합니다.
2. fn1이 **실행**됩니다.
3. fn1의 Functional Execution Context가 Call Stack에 push됩니다.
4. fn2이 **실행**됩니다.
5. fn2의 Functional Execution Context가 Call Stack에 push됩니다.
6. console.log가 **실행**됩니다.
7. console.log의 Functional Execution Context가 Call Stack에 push 됩니다.
8. console.log의 **실행이 완료**되며 console.log의 Functional Execution Context가 pop됩니다.
9. fn2의 **실행이 완료**되며 fn2의 Functional Execution Context가 pop됩니다.
10. fn1의 **실행이 완료**되며 fn1의 Functional Execution Context가 pop됩니다.
11. **앱 종료 시** Global Execution Context가 pop됩니다.

## Execution Context의 구성

```
ExecutionContext :{
    LexicalEnvironment:{
        Environment Records,
        Reference to the outer environment,
    },
    VariableEnvironment:{
        Environment Records,
        Reference to the outer environment,
    }
}
```

Execution Context는 LexicalEnvironment와 VariableEnvironment의 두 가지 구성으로 이루어지며, Environment들은 생성 시 같은 속성 카테고리를 가지고 있습니다. 각 Environment가 갖고 있는 공통된 내부 속성에 대해 먼저 알아보겠습니다.

#### 1. Reference to the outer environment

외부 환경 참조는 lexical scope를(정적 스코프)를 기준으로 상위 scope의 **Lexical Environment**를 참조합니다. 각 참조는 단방향 Linked List의 형태로 구현되어 있습니다.

![Context의 Linked List 구조](https://i.ibb.co/XZ3tvV2/image.png)

가장 먼저 생성되는 Global Execution Context는 외부 환경 참조 값으로 null을 갖습니다. 그리고 Functional Execution Context는 상위 Scope에 해당하는 Lexical Environment를 외부 환경 참조 값으로 갖습니다. 이 연결 고리는 변수 탐색 시 사용됩니다.

#### scope chain

```js
let name = "Jason";
function fn1() {
    console.log(name); //Jason
}
function fn2() {
    let name = "Peter";
    console.log(name); // Peter
    fn1();
}
fn2();
```

```
Global Lexical Environment: {
    ...,
    Reference to the outer environment: null
}

fn1 Lexical Environment: {
    ...,
    Reference to the outer environment:
        Global Lexical Environment
}

fn1 inner console.log Lexical Environment: {
    ...,
    Reference to the outer environment:
        fn1 Lexical Environment
}

fn2 Lexical Environment: {
    ...,
    Reference to the outer environment:
        Global Lexical Environment
}

fn2 inner console.log Lexical Environment: {
    ...,
    Reference to the outer environment:
        fn2 Lexical Environment
}
```

위 코드를 예시로 Reference to the outer environment의 Linked List가 사용되는 예시를 보겠습니다.

**fn1 내부의 console.log**는 name 변수 탐색 시 가장 먼저 자신의 Lexical Environment를 확인합니다. 그리고 name 변수를 찾지 못했을 경우 자신의 **외부 환경 참조**인 fn1의 Lexical Environment를 탐색하기 시작합니다. fn1에서도 name을 찾지 못했기 때문에 fn1의 외부 환경 참조인 Global Lexical Environment에서 탐색을 이어가고 결국 원하는 값을 찾게 됩니다. 이 참조 연결 고리는 목적인 변수를 찾아내거나 Global Lexical Environment에 다다를때까지 이어집니다.

유의할 점은 JS는 dynamic scope가 아닌 **lexical scope**를 따른다는 것입니다. 외부 환경 참조 값의 결정은 함수가 호출된 위치가 아닌 **함수가 선언된 위치**에 따라 결정됩니다. fn1이 fn2의 내부에서 호출되었지만 fn2의 name 변수 값을 사용하지 않고 Global의 name 값을 참조한 것을 보면 알 수 있습니다.

이것이 예전엔 **scope chain**이라고 부르던 js의 특성이며, 지금은 **Lexical nesting structure**라는 이름으로 불러지고 있습니다.

#### 2. Environment Record

Environment Record는 Lexical Environment 내에 식별자 바인딩을 기록하는 객체입니다. Environment Record를 상속하는 세개의 서브 클래스로 구성되어 있습니다.

```
Environment Record:{
    Declarative Environment Record,
    Object Environment Record,
    Global Environment Record
}
```

이 중 **Declarative Environment Record**에 <u>함수와 변수, this, super 등</u>의 식별자 바인딩이 저장되며, Variable Environment와 Lexical Environment는 각각 다른 방식으로 선언된 변수들을 관리합니다.

-   **Variable Environment**에는

    -   **var**로 선언된 변수가 메모리에 매핑되며 초기값으로 <u>undefined</u>가 할당됩니다. 변수 값 할당 코드가 실행되기 전 변수에 접근하게 되면 undefined 값을 얻게 됩니다. 할당 코드가 실행되고 난 뒤에는 해당 값으로 수정됩니다.

    -   **선언형 함수**가 메모리에 매핑되며 <u>함수 전체</u>가 메모리에 할당됩니다.

-   **Lexical Environment**에는

    -   **let, const**로 선언된 변수: 변수가 메모리에 매핑되지만 <u>초기값은 할당되지 않습니다</u>. 변수 값 할당 코드가 실행되기 전 변수에 접근하게 되면 reference error가 발생합니다. 초기 값 할당 코드가 실행되고 난 뒤에 메모리에 값이 추가 됩니다.

#### Lexical Environment vs Variable Environment?

우선 Variable Environment는 Lexical Environment를 **상속**하는 관계입니다. 그래서 Lexical Environment와 Variable Environment 모두 Lexical Environment라고 말할 수 있습니다. 그렇다면 왜 Variable Environment과 Lexical Environment를 구분짓는 것일까요?

**Lexical Environment**는 <u>let과 const로 선언된 변수</u>들을 위한 <u>local lexical scope</u>를 단위로 합니다. **Variable Environment**는 <u>var로 선언된 변수</u>들을 위한 <u>functional scope</u>를 단위로 합니다.

js에서 변수를 선언하는 방식인 **var**와 **let, const**의 차이를 복습해보겠습니다.

1. **hoisting**: var는 호이스팅이 되지만 let, const는 호이스팅 되지 않는다.
2. **scope**: var는 함수 단위 scope를 갖지만 let, const는 블록 단위 scope를 갖는다.

1번인 호이스팅의 원리는 Environment Record에서 선언 방식에 따라 어떻게 저장되는지의 차이로 반정도 설명이 되었습니다. 나머지 반은 Context의 생성 과정에서 마저 설명하겠습니다.

2번인 scope의 차이를 생각해보겠습니다. Reference to the outer environment에서 scope chain이 어떻게 구현되는지를 보았습니다. 하지만 var와 let, const는 다른 유효 scope를 갖고 있기 때문에 각 environment는 정의되는 시기와 외부 참조 변수의 정의에 차이가 있습니다. 아래 코드를 예시로 다시 한번 보겠습니다.

```js
function sayHiOneTime() {
    var isMorning = true;
    let hi = "Good morning!";
    while (isMorning) {
        var name = "Jack";
        let question = "How are you?";
        console.log(`${name} ${hi} ${question}`);
        isMorning = false;
    }
}
sayHiOneTime();
```

![Environment의 상세 구조](https://i.ibb.co/D8g82GZ/image.png)

sayHiOneTime은 호출되며 Execution Context를 생성합니다.

**Variable Environment**은 var가 유효한 함수 scope를 범위로 갖고 있기 때문에 sayHiOneTime의 scope 내에 있는 var로 선언된 isMorning과 name의 식별자 정보를 매핑합니다.

**Lexical Environment**은 block 단위의 scope를 갖기 때문에 시작은 Variable Environment와 같이 sayHiOneTime의 block을 범위로 갖습니다. 또 Environment Record에 let으로 선언된 hi의 정보를 매핑하고 있습니다. 그리고 sayHiOneTime엔 while block이 있습니다. while block은 다시 Lexical Environment를 정의하며, Environment Record에 question의 정보를 매핑합니다. 또 외부 환경 참조 값으로 sayHiOneTime의 Lexical Environment를 갖습니다.

## Context의 생성 과정

Execution Context는 Creation과 Execution의 두 단계를 거쳐 생성됩니다. 각 단계를 충분히 이해하는 것은 **hoisting**을 설명 할 때 거론되는 '끌어올림'이라는 의미를 이해하는데 큰 도움이 될 것입니다. 또 es3에서 **scope chain**으로 설명되던 개념이 es6에선 어떻게 구현되고 있는지 이해 할 수 있습니다.

### 1. Creation Phase

Creation 단계에선 **Lexical Environment**와 **Variable Environment**의 정의가 이루어집니다. This binding과 Outer Reference를 결정하고, Environment Record에 변수 식별자에 대한 메모리가 매핑되며 값의 할당은 선언 방식에 따라 다르게 이루어집니다.

-   Variable Environment에는

    -   var로 선언된 변수가 메모리에 매핑되며 초기값으로 undefined가 할당됩니다.
    -   선언형 함수가 메모리에 매핑되며 함수 전체가 메모리에 할당됩니다.

-   Lexical Environment에는
    -   let, const로 선언된 변수가 변수가 메모리에 매핑되지만 초기값은 할당되지 않습니다.

### 2. Execution Phase

Creation 단계에서 코드 실행을 위한 환경 정보 값이 결정되었다면, Execution은 코드를 위에서부터 읽으며 실행합니다. 변수 값이 할당되는 코드가 실행 될 경우 Environment Record에 저장된 식별자 메모리에 값을 수정 또는 할당합니다.

### hoisting

```js
console.log(v1); // undefined
console.log(v2); // Uncaught ReferenceError: v2 is not defined
var v1 = "notVisible1";
let v2 = "notVisible2";
```

js 엔진이 코드를 실행(Execution Phase)하기 전 코드의 실행 환경 정보를 구축(Creation Phase)하는 것이 **hoisting**이 이루어지는 이유입니다. hoisting을 설명 할 때 흔히 말해지는 '끌어올림'은 실질적으로 코드의 선언 줄이 변경되는 것이 아닌, Creation 단계에서 변수 식별자가 메모리에 우선적으로 매핑되는 특징을 말합니다.

예를 들어 let과 const로 선언된 변수의 식별자는 Creation Phase에서 메모리 매핑이되긴 하지만 코드 실행 전까지는 값이 할당되지 않습니다. 하지만 var로 선언된 v1의 경우 Creation Phase에서 메모리 매핑을 하며, 초기 값으로 undefined를 할당했기 때문에 오류 대신 undefined가 출력됩니다.

### 복습

아래 코드의 실행 시 Context 생성과 삭제 과정입니다.

```js
console.log(globalValue);
var globalValue = "nowVisible";

function sayHiOneTime() {
    var isMorning = true;
    let hi = "Good morning!";
    while (isMorning) {
        var name = "Jack";
        let question = "How are you?";
        console.log(`${name} ${hi} ${question}`);
        isMorning = false;
    }
}
sayHiOneTime();
```

![과정1](https://i.ibb.co/zFxvkTm/1.png)
![과정2](https://i.ibb.co/PwQ1dmK/2.png)
![과정3](https://i.ibb.co/Vpnsk7f/3.png)
![과정4](https://i.ibb.co/fYM1XBv/4.png)
![과정5](https://i.ibb.co/BGjP4WX/5.png)
![과정6](https://i.ibb.co/PzwKryV/6.png)
![과정7](https://i.ibb.co/3c3QzPq/7.png)
![과정8](https://i.ibb.co/10m21WY/8.png)

## [Posting Reference]

[자바스크립트 개발자라면 알아야 할 33가지 개념 #1 콜스택 (번역)](https://velog.io/@jakeseo_me/2019-03-15-2303-%EC%9E%91%EC%84%B1%EB%90%A8-rmjta5a3xh)  
[Call Stack과 Execution Context 를 알아보자](https://medium.com/sjk5766/call-stack%EA%B3%BC-execution-context-%EB%A5%BC-%EC%95%8C%EC%95%84%EB%B3%B4%EC%9E%90-3c877072db79)  
[자바스크립트의 The Execution Context (실행 컨텍스트) 와 Hoisting (호이스팅)](https://velog.io/@imacoolgirlyo/JS-%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8%EC%9D%98-Hoisting-The-Execution-Context-%ED%98%B8%EC%9D%B4%EC%8A%A4%ED%8C%85-%EC%8B%A4%ED%96%89-%EC%BB%A8%ED%85%8D%EC%8A%A4%ED%8A%B8-6bjsmmlmgy)  
[JavaScript 식별자 찾기 대모험](https://homoefficio.github.io/2016/01/16/JavaScript-%EC%8B%9D%EB%B3%84%EC%9E%90-%EC%B0%BE%EA%B8%B0-%EB%8C%80%EB%AA%A8%ED%97%98/)  
[Understanding Execution Context and Execution Stack in Javascript](https://blog.bitsrc.io/understanding-execution-context-and-execution-stack-in-javascript-1c9ea8642dd0)  
[The Ultimate Guide to Hoisting, Scopes, and Closures in JavaScript](https://ui.dev/ultimate-guide-to-execution-contexts-hoisting-scopes-and-closures-in-javascript/)  
[JavaScript: Execution Context, Hoisting, and Closures](https://medium.com/su-s-daily-log/javascript-execution-context-hoisting-and-closures-6d64cbcb6bc8)  
[Lexical Environment — The hidden part to understand Closures](https://medium.com/@5066aman/lexical-environment-the-hidden-part-to-understand-closures-71d60efac0e0)  
[Variable Environment vs lexical environment](https://stackoverflow.com/questions/23948198/variable-environment-vs-lexical-environment)  
[What's the difference between “LexicalEnvironment” and “VariableEnvironment” in spec](https://stackoverflow.com/questions/40691226/whats-the-difference-between-lexicalenvironment-and-variableenvironment-in)

**잘못된 작성된 정보에 대한 지적은 항상 감사합니다~!**
