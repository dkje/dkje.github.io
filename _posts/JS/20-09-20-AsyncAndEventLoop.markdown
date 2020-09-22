---
layout: post
title: "[JS] 비동기 실행과 Runtime"
subtitle: "  "
date: 2020-09-20 20:54:00
author: "Dev X"
header-img: "img/post-bg/post-bg-js.png"
published: true
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

100미터 달리기 훈련을 할당받은 선수가 셋 있습니다. 우리의 목표는 가장 짧은 시간 내에 훈련을 마치도록 하는 것입니다. 선수들이 훈련을 진행하는 방식은 두 가지로 나누어집니다.

-   **이어달리기(동기)**: 하나의 트랙에서 이어달리기로 훈련합니다. 각 선수들은 이전 선수가 달리기를 마쳐야만 출발 할 수 있습니다. 총 훈련에 6초가 걸립니다.

-   **각자 달리기(비동기)**: 선수들은 각자의 트랙에서 달리기를 시작합니다. 가장 느린 선수가 도착하는데 4.8초가 걸리므로 총 훈련에 4.8초가 걸렸습니다.

각 선수의 달리기 기량은 동기적으로 달리고 있는 선수들이 빠릅니다. 비동기적으로 연습한 선수들이 동기적으로 연습을 했었다면 총 11초의 시간이 걸렸을 것입니다. 트렉의 조건이 맞는다면 비동기적으로 훈련을 실행하는 것이 적합해보입니다.

---

**동기와 비동기**는 프로그래밍 코드의 <u>작업 처리 방식</u>, 특히 <u>데이터 통신</u>을 처리하는 방식을 설명할 때 중요하게 언급되는 개념입니다. 각 방식은 장단점이 있어 항상 어느 쪽이 좋다라고 말할 수 없습니다. 상황에 적절한 처리 방식으로 프로그램을 작성 할 수 있어야 합니다.

-   **동기적 실행**: **순차적**으로 작업이 실행됩니다. 이전 작업이 아직 실행 중이라면 다음 작업을 실행할 수 없습니다. 다음 작업을 하기 위해서, 이전 작업의 실행 결과가 필요할 때 적합합니다. JS는 일반적으론 동기 실행을 기반으로 합니다.

-   **비동기적 실행**: **비 순차적**으로 작업을 실행합니다. 이전 작업의 실행 여부가 다음 작업에 영향을 끼치지 않습니다. 작업이 완료되는데 시간이 걸리고, 실행 결과 값이 다음 작업에 영향을 끼치지 않는다면 비동기적으로 실행 할 수 있습니다.

JS는 엔진이 단 하나의 CallStack으로 코드를 실행하기 기본적으로 **동기 실행 언어**입니다. 하지만 비동기적 실행이 불가능한 것은 아닙니다. JS에서 비동기적 작업을 처리하기 위해서는 CallStack만이 아닌 **Callback Queue**와 **EventLoop**의 도움이 필요합니다.

### ✋블로킹(Blocking) 논블로킹(Nonblocking)

동기와 비동기를 설명 할 때 함께 자주 언급되는 개념에 **블로킹**과 **논블로킹**이 있습니다. 동기 실행이 블로킹이고 비동기 실행이 논블로킹이다라고 오해하기 쉽지만, 각각의 개념은 상응되지 않습니다.

-   **블로킹**: 진행 중인 작업이 실행 제어권을 갖고 있기 때문에, 다음 작업이 대기 중인 상태.
-   **논브로킹**: 진행 작업이 바로 다음 작업에게 실행 제어권을 넘기기 때문에 바로 다음 작업을 실행 할 수 있는 상태.

하지만 동기 방식도 논블로킹일 수 있고, 비동기 방식도 블로킹 될 수 있습니다.

-   **Sync & Blocking**: 동기적으로 작업을 실행합니다. 실행 권한이 현 작업에게 있는 동안 다음 작업이 대기하며 블로킹이 발생합니다.
-   **Async & Blocking**: 비동기적으로 작업을 실행했으나, 실행 제어권을 비동기 실행 구문이 갖고 있다면 블로킹이 발생합니다. (Sync와 차이가 없기 때문에 Async를 잘 못 사용한 경우)
-   **Sync & Nonblocking**: 동기적으로 작업을 실행했으나, 바로 실행 제어권을 다음 작업에게 전달합니다. 다른 작업을 실행하며 일찍 권한을 넘긴 실행 문이 완료되었는지 확인해야 합니다.
-   **Async & Nonblocking**: 비동기적으로 작업을 실행하고, 실행 제어권이 바로 다음 작업에게 전달됩니다. 비동기 작업이 완료되고 필요에 따라 Callback 함수를 동기 실행 환경에 호출합니다.

중요한 것은 <u>비동기로 작업을 실행하는 것이 반드시 논블로킹 코드가 아니라는 것</u>입니다.

참고하면 좋을 글 : [Blocking-NonBlocking-Synchronous-Asynchronous](https://homoefficio.github.io/2017/02/19/Blocking-NonBlocking-Synchronous-Asynchronous/)

## ⛺JS Runtime

웹 브라우저는 JS 엔진만으로 실행되지 않습니다. 브라우저가 제공하는 JS 런타임 환경이 **Web API**, **Queue**, **Event Loop**등으로 이루어져 있는 덕분에 JS의 비동기적 실행이 가능합니다.

![런타임의 전체구조](https://i.ibb.co/0Fw3xXP/image.png)

### 🔧JS Engine(Single Thread)

JS 엔진은 웹 페이지의 JS 코드를 수신받아 구문을 분석하고, 구문 오류가 없다면 실행 가능한 명령으로 변환합니다. JS엔진은 Memory Heap과 CallStack으로 구성되었습니다.

-   **Memory Heap**: 메모리 힙은 <u>변수 및 객체들을 할당</u>하는 공간입니다. JS의 메모리는 수동 할당과 해제가 불가능하며 대신 가비지 콜렉터로 관리됩니다.

-   **CallStack**: 콜 스택은 <u>함수의 호출을 Stack으로 관리하는 공간</u>입니다. JS Engine은 단 하나의 CallStack을 갖고 있기 때문에 <u>싱글 스레드의 동기적 방식</u>으로 실행됩니다. 함수는 호출 시 CallStack에 추가(push)됩니다. 최상위의 stack 함수가 실행되는 동안 하위의 함수들은 작업을 멈춘 채 대기합니다. 최상위의 함수가 작업을 완료한 후엔 CallStack에서 제거(pop)되며 return 값과 함께 이전 실행 함수로 되돌아갑니다. 콜 스택의 더 자세한 실행 과정은 [이전 글](https://dkje.github.io/2020/08/30/ExecutionContext/)에 정리되어있습니다.

가장 대표적인 JS 엔진은 크롬과 Node.js에서 사용하는 V8이 있습니다. 그 외에도 Chakra, Rhino등 실행 브라우저와 프로그램에 따라 다양한 엔진들이 있습니다.

#### JS Engine과 JS Runtime의 차이

-   **JS 엔진**은 코드를 분석, 실행하는 인터프리터(Interpreter)와 JIT 컴파일러입니다. JS 코드를 실행 가능한 기계어로 변환합니다.

-   **Runtime**은 API와 EventLoop, Queue 포함해 코드가 실행되는 환경에 따라 추가적 기능을 포함합니다.
    -   브라우저: DOM API, Fetch, Timer, LocalStorage ...
    -   서버: File System Access, Network Access, Console ...
    -   데스크탑: GUI, File System, Network Access ...

### 🌍Web API(Multi Thread)

[웹 API](https://developer.mozilla.org/en-US/docs/Web/API)는 브라우저에 내장되어 작업을 실행하기 위한 다양한 추가 기능을 제공합니다. 웹 API기능의 다수는 blocking이 일어나는 것을 방지하기 위해 비동기로 작동합니다. 대표적인 API들의 예시입니다.

-   **Document API(DOM)**: 문서를 조작하기 위해 제공하는 API입니다. HTML 생성, 제거, 변경과 CSS의 조작을 페이지에 동적으로 적용 할 수 있게 합니다.

-   **Fetch Data API**: 서버로부터 data를 수신받아, 페이지 전체를 새로고침하지 않고 일부만 업데이트 할 때 사용하는 API입니다. 이런 기법을 AJAX(Asynchronous JavaScript and XML, 비동기 자바스크립트와 XML)이라고 부르며 XMLHttpRequest과 Fetch API등이 속합니다.

-   **Drawing & Graphics API**: HTML의 Canvas 요소에 픽셀 데이터를 업데이트 해 2D 또는 3D 이미지를 구현할 수 있습니다. Canvas와 WebGL등이 속합니다.

-   **Device API**: 하드웨어가 제공하는 기능에 접근하기 위한 API입니다. 기기의 지원 여부에 따라 GPS, Bluetooth, Battery API 등의 기능을 사용할 수 있습니다.

-   **Client-side Storage API**: 클라이언트 측에 데이터를 저장하는 기능을 제공합니다. Storage를 통해 지속 사용할 상태를 저장하거나, 장치가 오프라인일 때도 실행 가능한 기능을 구현 할 수 있습니다. WebStorage API등이 속합니다.

### 🎱Queue

<u>Web API에서 실행 완료 후 전달된 Task(callback)가 CallStack에 추가되기 전 대기</u>하는 곳입니다. Event Loop가 Task를 실행 할 수 있는 특정 시점을 확인 했을 때, Runtime은 Queue에 가장 오래된 Task를 제거(dequeue)하고 CallStack에 추가합니다. Queue에 나머지 Task가 있다면 이벤트 루프가 반복합니다.

JS Runtime에는 여러가지 Queue가 존재합니다. API의 종류에 따라 각자의 Queue에 관리가 되며 여러 Queue에 Task가 대기 시 Event Loop는 <u>우선 순위가 높은 Queue의 Task를 먼저 처리합니다</u>.

-   **MicroTask Queue**(Job Queue): MicroTask는 CallStack이 비고 난 후 최우선적으로 처리됩니다. Promise, process.nextTick, Object.observe, MutationObserver등이 속합니다.
-   **Task Queue**: <u>MicroTask Queue가 완전히 비고 난 뒤에</u> Event Loop에 의해 처리됩니다. 스크립트 실행, fetch, Ajax, DOM, 등이 속합니다.
-   **Animation frames**: <u>MicroTask Queue가 완전히 비고 난 뒤에</u> Event Loop에 의해 처리됩니다.

MicroTask Queue는 항상 최우선 순위를 갖고 있지만, Task Queue와 Animation frames의 우선 순위는 브라우저에 따라 실행 결과에 차이가 있었습니다.

-   Chrome, Whale, Edge: Microtask Queue > Animation frames > Task
-   Firefox: Microtask Queue > Task > Animation frames

```js
// 테스트용 코드
console.log("start");

requestAnimationFrame(function () {
    console.log("Animation Frames Run");
});

setTimeout(() => {
    console.log("Task Run");
}, 0);

new Promise((resolve) => {
    console.log("promise run");
    resolve();
})
    .then(() => {
        console.log("Microtask1 run");
    })
    .then(() => {
        console.log("Microtask2 run");
    });

console.log("end");
```

### 💫Event Loop(Single Thread)

이벤트 루프는 Queue의 Task를 CallStack에 전달하는 중계자 역할을 합니다. 이벤트 루프는 반복적으로 실행되며 모든 Queue가 빈 상태라면 **null**로 초기화됩니다. 이벤트 루프의 실행 과정은 추상적으로 아래와 같이 표현됩니다.

```js
while (runningProgram) {
    if (callStack.hasNothingTodo) {
        eventLoop.doMicroTaskCheckPoint();
        if (microtaskQueue.hasNoTask) {
            eventLoop.doTaskCheckPoint();
            eventLoop.doAnimationFramesCheckPoint();
        }
    }
}

Queue.prototype.doCheckPoint = function () {
    if (Queue.hasNoTask) {
        return;
    }
    eventLoop.task = Queue.dequeue();
    eventLoop.sendTaskToCallStack();
    eventLoop.task = null;
};
```

<span style="color:grey">checkpoint는 MicroTask를 대상으로만 서술 되어있지만 eventLoop가 실행되는 과정이 유사하기에 인터페이스로 표현했습니다. [HTML5 spec 문서](https://html.spec.whatwg.org/multipage/webappapis.html#perform-a-microtask-checkpoint)<span>

1. callStack에 실행 중인 함수가 없다.
2. Queue에 Task가 존재한다.

위의 두 조건이 모두 true일 때, 이벤트 루프는 **doCheckPoint**의 과정을 통해 Queue의 가장 오래된 Task를 CallStack으로 전달합니다. checkPoint는 우선 순위가 높은 Queue를 대상으로 먼저 실행하며, Queue가 완전히 비었을 때 차순위 Queue의 checkpoint가 실행됩니다. 예를들어, 차순위 Task가 대기 중인 상황에 MicroTask가 추가된다면 MicroTask를 우선적으로 처리합니다.

## 비동기 실행시 Runtime의 동작

<iframe src="https://slides.com/kim3333/deck/embed" width="100%" height="500px" scrolling="no" frameborder="0" webkitallowfullscreen mozallowfullscreen allowfullscreen></iframe>

**요약**

-   callStack(Single Thread): 동기적으로 작업을 실행한다. 필요에 따라 Web API를 호출.
-   WebAPI(Multi Thread): 여러 쓰레드를 이용해 비동기적으로 작업을 실행한다. 작업이 완료되면 Task(callback)를 Queue로 전달한다.
-   Queue: WebAPI에서 작업을 마친 Task들이 대기하는 Container. 여러개가 존재하며 Queue에 따라 처리 우선 순위가 있다.
-   EventLoop: callStack의 작업을 모두 마치고 나면 최우선 순위의 Task를 callStack에 추가한다.

## [Posting Reference]

[Introduction to web APIs](https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Client-side_web_APIs/Introduction)  
[어쨌든 이벤트 루프는 무엇입니까? ](https://www.youtube.com/watch?v=8aGhZQkoFbQ&ab_channel=JSConf)  
[자바스크립트 런타임](https://beomy.github.io/tech/javascript/javascript-runtime/)  
[Blocking-NonBlocking-Synchronous-Asynchronous](https://homoefficio.github.io/2017/02/19/Blocking-NonBlocking-Synchronous-Asynchronous/)  
[Web application APIs - 8.1.6 Event loops](https://html.spec.whatwg.org/multipage/webappapis.html#task-queue)  
[Javascript Runtime 의 구조](http://jaynewho.com/post/25)  
[Event Loop (이벤트 루프)](https://velog.io/@thms200/Event-Loop-%EC%9D%B4%EB%B2%A4%ED%8A%B8-%EB%A3%A8%ED%94%84)  
[How JavaScript works: an overview of the engine, the runtime, and the call stack](https://blog.sessionstack.com/how-does-javascript-actually-work-part-1-b0bacc073cf)  
[비동기와 Promise #3](https://blog.javarouka.me/2016/11/12/javascript-async-promise-3/)
