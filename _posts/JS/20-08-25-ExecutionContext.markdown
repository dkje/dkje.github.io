---
layout: post
title: '[JS]Execution Context와 Call Stack'
subtitle: '덤으로 hoisting까지'
date: 2020-08-25 23:21:00
author: 'Dev X'
header-img: 'img/post-bg/post-bg-js.png'
published: false 
catalog: true
tags:
    - js
    - Execution Context
    - Call Stack
    - hoisting
---

**Execution Context**는 scope와 hoisting, this, closure등을 관통하는 가장 근본적인 개념입니다. 자바스크립트 코드의 실행 흐름과 코드에 대한 정보가 Execution Context로 관리되기 때문에 Execution Context를 이해하지 못하면 부수적인 개념들에 대해서도 완벽하게 이해 할 수 없습니다.

이 글은 Execution Context의 정의와 자바스크립트 엔진이 Execution Context를 관리하는 흐름에 대해 공부하며 정리했습니다

**ECMA-262 6th를 기준으로 작성된 글입니다**

## Execution Context(실행 컨텍스트)란?

자바스크립트 엔진이 코드를 실행하기 위해선 <u>코드에 대한 정보</u>들이 필요합니다. 코드에 선언된 변수와 함수, 스코프, this, arguments 등을 묶어, <u>코드가 실행되는 위치를 설명한다는 뜻의</u> **Execution Context**라고 부릅니다. 자바스크립트 엔진은 Execution Context를 객체로 관리하며 코드를 Execution Context 내에서 실행합니다.

## Execution Context의 종류

실행 컨텍스트의 종류는 아래의 세 가지로 나누어집니다.

**1. Global Execution Context**  
코드를 실행하며 **가장 처음, 단 한 개만** 생성되는 전역 Context입니다. 생성 시 global object를 생성하며 this 값에 global object를 참조합니다. 전역 실행 컨텍스트는 Call Stack의 가장 바닥에서 유지되며 앱이 종료 될 때 삭제됩니다.

**2. Functional Execution Context**  
함수가 **실행** 될 때 마다 생성되는 Context입니다. 전역 실행 컨텍스트가 단 한 번만 생성되는 것과 달리, 함수 실행 컨텍스트는 매 실행시마다 새로 생성되며 함수 실행이 종료(return)되면 Call Stack에서 제거됩니다.

**3. Eval Context**  
[eval](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/eval) 함수로 실행한 코드의 Context입니다. 보안상 취약한 점이 있어 비권장 함수이기 때문에 이 글에서는 다루지 않습니다.


## Execution Context의 관리: CallStack


js 엔진은 생성된 Context를 관리하는 목적의 **Call Stack**(호출스택)을 갖고 있습니다. Call Stack은 단일 스레드 형식이기 때문에 단 하나의 Call Stack을 갖고 있습니다. (js가 동기 언어인 이유입니다) js 엔진은 처음 코드를 실행 했을 때 Global Execution Context를 생성해 stack에 가장 먼저 쌓으며, 함수가 실행 또는 종료 될 때마다 stack을 push(추가), pop(제거)합니다.

```js

let name = 'Jason'

function fn1(){
    fn2()
}

function fn2(){
    console.log(`Hi! my name is ${name}`);
}

fn1();

```

1. 코드의 전역 범위가 실행 될 때 Global Execution Context를 생성 후 Call Stack에 push합니다.
2. fn1이 실행되며 fn1의 Functional Execution Context가 Call Stack에 push됩니다.
3. fn2이 실행되며 fn2의 Functional Execution Context가 Call Stack에 push됩니다.
4. fn2의 실행이 완료되며 fn2의 Functional Execution Context가 Call Stack에서 pop됩니다.
5. fn1의 실행이 완료되며 fn1의 Functional Execution Context가 Call Stack에서 pop됩니다.
6. 앱 종료 시 Global Execution Context가 Call Stack에서 pop됩니다.
   

## Execution Context의 구성

Execution Context는 LexicalEnvironment와 VariableEnvironment의 두 가지 속성을 갖고 있습니다. 

```
ExecutionContext :{
    LexicalEnvironment:{
        Environment Records, 
        Reference to the outer environment,
        This binding
    },
    VariableEnvironment:{
        Environment Records, 
        Reference to the outer environment,
        This binding
    }
}
```


## Context의 생성 과정

1. Creation Phase

    > Lexical Environment 생성 (Execution Inception)  
    > Variable Environment 생성 (Memory)

2. Execution Phase

    >

Execution Context는 Creation과 Execution의 두 단계를 거쳐 생성됩니다. 각 단계를 충분히 이해하는 것은 **hoisting**을 설명 할 때 거론되는 '끌어올림'이라는 의미를 이해하는데 큰 도움이 될 것입니다.

### 1. Creation 단계

Creation 단계에선 **Lexical Environment**와 **Variable Environment**의 생성이 이루어집니다.

#### Lexical Environment

Lexical Environment는 3가지 컴포넌트를 갖고 있습니다.

1. Environment Record(환경 정보):
2. Reference to the Outer Environment(외부 환경 참조): 바깥의 Lexical Environment에 대한 정보를 저장합니다.
3. This Binding: this 값을 설정합니다.

#### Variable Environment

생성 단계

-   arguments, function가 메모리에 매핑됩니다.
-   var로 선언된 변수들이 메모리에 매핑되며 undefiend가 값으로 할당됩니다.
-   this가 설정됩니다.

실행단계

-   변수에 값이 할당됩니다
-

**Creation 단계에서 생성되는 Context 정보에 대한 요약입니다.**

-   [Global Context]라면 **Global Object**를 생성한다
-   [Functional Context]라면 **arguments** 객체를 생성한다
-   **this**를 생성한다
-   **선언식 함수**를 메모리에 할당한다
-   **var**로 선언된 **변수**의 메모리를 준비해 우선 undefiend를 할당한다

### 2. Execution 단계

Execution Context에는 Global Context(전역 컨텍스트)와 Functional Context(함수 컨텍스트)의 두 가지 유형이 있습니다.

## Execution Context의 종류

### Global Context(전역 컨텍스트)

Global Context는 코드가 처음 실행될 때 생성되는 Context입니다. Global Context는 초기 화시 두개의 global object와 this를 생성합니다.

(브라우저에서 global object는 window이고, this는 global object인 window를 참조합니다)

### Functional context

## CallStack

callstack과 heap, task queue의 구조

**[Posting Reference]**  
[자바스크립트는 어떻게 작동하는가: 엔진, 런타임, 콜스택 개관](https://engineering.huiseoul.com/%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8%EB%8A%94-%EC%96%B4%EB%96%BB%EA%B2%8C-%EC%9E%91%EB%8F%99%ED%95%98%EB%8A%94%EA%B0%80-%EC%97%94%EC%A7%84-%EB%9F%B0%ED%83%80%EC%9E%84-%EC%BD%9C%EC%8A%A4%ED%83%9D-%EA%B0%9C%EA%B4%80-ea47917c8442)  
[자바스크립트 개발자라면 알아야 할 33가지 개념 #1 콜스택 (번역)](https://velog.io/@jakeseo_me/2019-03-15-2303-%EC%9E%91%EC%84%B1%EB%90%A8-rmjta5a3xh)  
[Call Stack과 Execution Context 를 알아보자](https://medium.com/sjk5766/call-stack%EA%B3%BC-execution-context-%EB%A5%BC-%EC%95%8C%EC%95%84%EB%B3%B4%EC%9E%90-3c877072db79)  
[자바스크립트의 The Execution Context (실행 컨텍스트) 와 Hoisting (호이스팅)](https://velog.io/@imacoolgirlyo/JS-%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8%EC%9D%98-Hoisting-The-Execution-Context-%ED%98%B8%EC%9D%B4%EC%8A%A4%ED%8C%85-%EC%8B%A4%ED%96%89-%EC%BB%A8%ED%85%8D%EC%8A%A4%ED%8A%B8-6bjsmmlmgy)  
[Javascript Fundamentals — Call Stack and Memory Heap](https://medium.com/@allansendagi/javascript-fundamentals-call-stack-and-memory-heap-401eb8713204)  
[Understanding Execution Context and Execution Stack in Javascript](https://blog.bitsrc.io/understanding-execution-context-and-execution-stack-in-javascript-1c9ea8642dd0)  
[The Ultimate Guide to Hoisting, Scopes, and Closures in JavaScript](https://ui.dev/ultimate-guide-to-execution-contexts-hoisting-scopes-and-closures-in-javascript/)  
[JavaScript: Execution Context, Hoisting, and Closures](https://medium.com/su-s-daily-log/javascript-execution-context-hoisting-and-closures-6d64cbcb6bc8)  
[Lexical Environment — The hidden part to understand Closures](https://medium.com/@5066aman/lexical-environment-the-hidden-part-to-understand-closures-71d60efac0e0)
