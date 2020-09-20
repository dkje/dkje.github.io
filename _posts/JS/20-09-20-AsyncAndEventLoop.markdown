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

**동기와 비동기**는 프로그래밍 언어의 실행 방식, 특히 데이터를 처리하는 방식을 설명할 때 중요하게 언급되는 개념입니다.

-   동기적 실행:
-   비동기적 실행:

JS는 기본적으로 **동기 실행 언어**입니다. JS 엔진이 단 하나의 CallStack으로 코드를 실행하기 때문입니다. 하지만 비동기적 실행이 불가능한 것은 아닙니다.

### ✋블로킹(Blocking) 논블로킹(Nonblocking)

동기와 비동기를 설명 할 때 함께 자주 언급되는 개념에 **블로킹**과 **논블로킹**이 있습니다. 동기 실행이 블로킹이고 비동기 실행이 논블로킹이다라고 오해하기 쉽지만, 각각의 개념은 상응하지 않습니다.

-   블로킹: 이전 실행 작업이 완료되기를 대기하는 상태.
-   논브로킹: 이전 실행 작업의 상태와 상관 없이 바로 작업을 실행 할 수 있는 상태.

## JS 런타임

### JS Engine(V8)

### Web API(Browser API)

### Callback queue

### Event Loop

## CallStack과 동기적 실행

## EventLoop와 비동기적 실행

## [Posting Reference]

[Introduction to web APIs](https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Client-side_web_APIs/Introduction)  
[동시성 모델과 이벤트 루프](https://developer.mozilla.org/ko/docs/Web/JavaScript/EventLoop)  
[어쨌든 이벤트 루프는 무엇입니까? ](https://www.youtube.com/watch?v=8aGhZQkoFbQ&ab_channel=JSConf)  
[자바스크립트 런타임](https://beomy.github.io/tech/javascript/javascript-runtime/)
