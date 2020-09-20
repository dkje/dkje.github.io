---
layout: post
title: "[JS] 동시성과 이벤트 루프"
subtitle: "  "
date: 2020-09-20 20:54:00
author: "Dev X"
header-img: "img/post-bg/post-bg-js.png"
published: false
catalog: true
tags:
    - JS
    - javascript
    - Event Loop
    - runtime
    - web api
---

JS Engine은 동기 방식만을 지원하기 때문에 비동기 방식의 흐름을 이해하려면 런타임의 전체적 그림을 이해할 수 있어야 합니다. 이 글은 웹 기준, 동기와 비동기에 대한 이해를 목적으로 정리되었습니다.

## 🏃‍♀️동기(sync) 비동기(async)

![동기와 비동기 예시그림](https://i.ibb.co/kyyspHH/1-1.gif)

100미터 달리기 훈련을 할당받은 선수가 셋 있습니다. 우리의 목표는 가장 짧은 시간 내에 훈련을 마치도록 하는 것입니다. 선수들이 훈련을 진행하는 방식은 두 가지로 나누어 집니다.

-   **이어달리기(동기적 연습)**: 하나의 트랙에서 이어달리기로 훈련합니다. 각 선수들은 이전 선수가 달리기를 마쳐야만 출발 할 수 있습니다. 총 훈련에 6초가 걸립니다.

-   **각자 달리기(비동기적 연습)**: 선수들은 각자의 트랙에서 달리기를 시작합니다. 가장 느린 선수가 도착하는데 4.8초가 걸리므로 총 훈련에 4.8초가 걸렸습니다.

트렉의 조건이 맞는다면 비동기적으로 훈련을 실행하는 것이 적합해보입니다.

---

**동기와 비동기**는 프로그래밍 코드의 작업 처리 방식, 특히 데이터 통신을 처리하는 방식을 설명할 때 중요하게 언급되는 개념입니다. 각 방식은 장단점이 있어 항상 어느 쪽이 좋다라고 말할 수 없습니다. 상황에 맞는 처리 방식을 선택해야 합니다.

-   **동기적 실행**: **순차적**으로 작업이 실행됩니다. 이전 작업이 아직 실행 중이라면 다음 작업을 실행할 수 없습니다. 다음 작업을 하기 위해서, 이전 작업의 실행 결과가 필요할 때 적합합니다. JS는 일반적으론 동기 실행을 기반으로 합니다.

-   **비동기적 실행**: **비 순차적**으로 작업을 실행합니다. 이전 작업의 실행 여부가 다음 작업에 영향을 끼치지 않습니다. 작업이 완료되는데 시간이 걸리고, 실행 결과 값이 다음 작업에 영향을 끼치지 않는다면 비동기적으로 실행 할 수 있습니다.

JS는 엔진이 단 하나의 CallStack으로 코드를 실행하기 기본적으로 **동기 실행 언어**입니다. 하지만 비동기적 실행이 불가능한 것은 아닙니다. JS에서 비동기적 작업을 처리하기 위해서는 CallStack만이 아닌 **Callback Queue**와 **EventLoop**의 도움이 필요합니다.

### ✋블로킹(Blocking) 논블로킹(Nonblocking)

동기와 비동기를 설명 할 때 함께 자주 언급되는 개념에 **블로킹**과 **논블로킹**이 있습니다. 동기 실행이 블로킹이고 비동기 실행이 논블로킹이다라고 오해하기 쉽지만, 각각의 개념은 상응되지 않습니다.

-   **블로킹**: 진행 중인 작업이 실행 제어권을 갖고 있기 때문에, 다음 작업이 대기 중인 상태.
-   **논브로킹**: 진행 작업이 바로 다음 작업에게 실행 제어권을 넘기기 때문에 바로 다음 작업을 실행 할 수 있는 상태.

하지만 동기 방식도 논블로킹일 수 있고, 비동기 방식도 블로킹 될 수 있습니다.

-   Sync & Blocking: 동기적으로 작업을 실행. 실행 권한이 현 작업에게 있는 동안 다음 작업이 대기하며 블로킹이 발생함. 
-   Async & Blocking: 비동기적으로 작업을 실행했으나, 실행 제어권을 비동기 실행 구문이 갖고 있다면 블로킹이 발생함. (Sync와 차이가 없기 때문에 Async를 잘 못 사용한 경우)
-   Sync & Nonblocking: 동기적으로 작업을 실행했으나, 바로 실행 제어권을 다음 작업에게 넘김. 다른 작업을 실행하며 일찍 권한을 넘긴 실행 문이 완료되었는지 확인해야 함.
-   Async & Nonblocking: 비동기적으로 작업을 실행. 실행 제어권이 바로 다음 작업에게 넘어감. 비동기 작업이 완료되고 필요에 따라 Callback 함수를 동기 실행 환경에 호출.

참고하면 좋을 글 : [Blocking-NonBlocking-Synchronous-Asynchronous](https://homoefficio.github.io/2017/02/19/Blocking-NonBlocking-Synchronous-Asynchronous/)

## JS 런타임

브라우저에서 JS는 JS 엔진만으로 실행되지 않습니다. JS 런타임은 Web API와 Callback Queue, Event Loop라는 추가 부품들로 이루어져 있고, 코드를 실행하기 위해선 반드시 이 부품들의 도움이 필요합니다.

### JS Engine(V8)

JS 엔진은 JS 코드를 실행하는 프로그램을 말합니다.
Memory Heap과 CallStack으로 구성되었습니다.

- Memory Heap:
- CallStack:

가장 대표적인 JS 엔진은 크롬과 Node.js에서 사용하는 V8이 있습니다. 그 외에도 Chakra, Rhino등 다양한 엔진들이 있습니다.

### Web API(Browser API)

웹 브라우저에서 JS를 실행하기 위해 추가적으로 필요한 기능들이 있습니다. DOM과 Timer, 등의 기능이 Web API에 있습니다.


### Callback queue

Web API에서 실행된 작업이 CallStack으로 돌아가기 전 대기하는 곳입니다.

-Render Queue:
-Animation Queue:

### Event Loop

Callback Queue에 대기 중인 작업들을 CallStack에 추가해도 될지를 반복적으로 확인합니다. CallStack에 아직 실행 중인 작업이 있다면 Queue의 작업들을 계속 대기시키다가, CallStack의 모든 작업이 완료되어 다른 작업을 추가 할 수 있는 상태 일 때 작업을 이동시킵니다.

## CallStack과 동기적 실행

## EventLoop와 비동기적 실행

## [Posting Reference]

[Introduction to web APIs](https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Client-side_web_APIs/Introduction)  
[동시성 모델과 이벤트 루프](https://developer.mozilla.org/ko/docs/Web/JavaScript/EventLoop)  
[어쨌든 이벤트 루프는 무엇입니까? ](https://www.youtube.com/watch?v=8aGhZQkoFbQ&ab_channel=JSConf)  
[자바스크립트 런타임](https://beomy.github.io/tech/javascript/javascript-runtime/)  
[Blocking-NonBlocking-Synchronous-Asynchronous](https://homoefficio.github.io/2017/02/19/Blocking-NonBlocking-Synchronous-Asynchronous/)  