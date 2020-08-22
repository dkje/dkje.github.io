---
layout: post
title: '[JS] 5가지 패턴으로 this 문맥 읽기'
subtitle:
date: 2020-08-18 09:13:00
author: 'Dev X'
header-img: 'img/post-bg/post-bg-js.png'
published: true
catalog: true
tags:
    - js
    - this
    - strict mode
    - arrow function
---

js을 배우기 전에 다른 언어를 배웠던 사람이라면 js의 this를 보고 큰 혼란에 빠지곤 합니다. 타 언어에서는 this는 '내가 속한 객체'를 참조하지만 js에서 this는 '함수를 호출한 방식'에 따라 참조하는 객체가 달라지죠. js에서 this는 코드의 흐름을 읽기 전엔 무엇인지 정확하게 알기 어렵습니다.

## Execution Context(실행 컨텍스트)

우리가 js 코드를 실행시키면 js 엔진은 코드를 실행하기 위한 정보를 정리합니다. 실행 컨텍스트는 <u>코드가 실행되고 있는 환경</u>을 뜻하는 추상적 개념입니다. 우리가 js 코드를 실행시키는 순간부터 실행 컨텍스트는 variable, scope chain, <u>this</u> 등을 갖고 생성됩니다.

context는 **Global context**(전역 컨텍스트)와 **Functional Context**(함수 컨텍스트)의 두 가지가 있습니다. 전역 컨텍스트는 코드를 실행하자마자 생성되며, <u>코드 전체에 대한 정보</u>를 갖고 있습니다. 함수 컨텍스트는 <u>함수가 실행 될 때마다 생성</u>되는 독자적 실행 컨텍스트입니다.

간단하게 나마 this가 실행 컨텍스트가 갖고 있는 객체라는 것을 알게 되었습니다. 그렇다면 실행 컨텍스트는 this를 어떻게 정하는 걸까요?

## 전역 컨텍스트 this

```js
console.log(this);
> Window {parent: Window, opener: null, top: Window, …}
```

코드가 실행 된 후 정해지는 실행 컨텍스트는 global 객체를 가리킵니다. 위의 코드는 크롬 개발자 도구의 전역 스코프에서 this를 출력한 결과입니다. 브라우저의 global object는 window이기 때문에 this는 window를 가리키고 있습니다.

## 함수 컨텍스트 this

함수 내의 this는 함수를 호출한 방식에 따라 달라집니다. js에선 this의 문맥이 달라지는 다섯 가지 함수 호출 실행이 있습니다. **regular function, call과 apply, method, constructor** 의 경우이죠. 각 호출방식이 어떻게 this를 정하는 지 하나씩 살펴보겠습니다.

### 1. 일반 함수 호출 시

```js
function clgThis() {
    console.log(this); // window
}
clgThis();

function clgChar() {
    let char = 'A';
    this.char = 'B';
    console.log(char); //A
}
clgChar();
```

선언식 또는 표현식으로 선언된 함수의 실행시 this는 **global object**를 참조합니다. 위 코드에서 볼 수 있듯 함수 내에서도 this는 여전히 window 객체를 가리키고 있기 때문에 this.char라는 변수에 'B'를 할당했을 시에도 함수 스코프 내의 char가 아닌 global object인 window 객체에 char라는 속성이 추가됐을 뿐이죠.

#### strict mode

```js
'use strict';
let global = this;
function clgThis() {
    console.log(this); // undefiend
    console.log(global); //window
}
clgThis();
```

es5에 추가된 문법 중 **strict mode**(엄격 모드)라는 것이 있습니다. js는 느슨한 언어라는 것이 장점이지만 느슨한 덕에 무시된 에러들을 정확하게 파악하기 힘들다는 단점이 있기도 하죠. strict 모드는 무시되던 오류들을 throw해, 실수로 의도치 않게 일어날 수 있는 문제들을 방지합니다.

strict mode가 바로 잡는 실수 중엔 일반 함수 내의 this 사용이 있습니다. 위의 코드를 보면, 전역에서 사용된 this는 여전히 window를 참조하고 있지만 일반 함수 호출시 사용된 this는 **undefiend**를 참조하고 있습니다. js의 this 생성 규칙에 익숙하지 않은 사람이 일반 함수 호출에서 this를 사용했을시 window를 참조하게 되는 실수를 방지하기 위함이죠. 만약 strict mode의 일반 함수 호출 내에서 global this를 사용하고 싶다면 전역 변수로 global 객체에 this를 할당해 일반 함수 내에서 사용해야 합니다.

### 2. 메서드 호출 시

```js
const person = {
    name: 'Sam',
    sayHi() {
        console.log(`Hellow~ My name is ${this.name}!`);
        //Hellow~ My name is Sam! // this는 person
        function innerFunc() {
            console.log(`Hellow~ My name is ${this.name}!`);
            // Hellow~ My name is ! // this 는 window
        }
        innerFunc();
    },
};
person.sayHi();
```

메서드는 **객체에 종속된 함수**를 뜻합니다. 메서드 내에서 this는 <u>자신을 소유하고 있는 객체</u>를 가리킵니다. sayHi는 person에 속해졌기 때문에 위의 person.sayHi의 this는 person을 참조합니다.

여기까진 직관적으로 이해가 가능했지만, 우리를 혼란스럽게 만드는 것은 **메서드 스코프 내의 함수가 참조하는 this**입니다. person.sayHi 메서드 내의 innerFunc 함수는 호출되며 <u>일반 함수의 컨텍스트를 생성</u>했기 때문에 person.sayHi의 this가 person에 바인딩되어 있는 것과 관련 없이 **window**를 가리킵니다. 타 언어의 this에 익숙했던 사람은 자연스럽게 innerFunc이 생성된 객체를 찾다가 혼란에 빠지기 쉬운 차이입니다. 그렇다면 메서드 내의 함수에서 메서드의 this를 사용하려면 어떤 방법이 있을까요? 1번에서 했듯 메서드 내에 지역 변수로 this를 할당받아 사용하는 방법도 있겠지만 명시적으로 사용하고자하는 this를 함수에 바인딩 하는 방법도 있습니다.

### 3. call, apply 호출 시

**또는 bind된 함수 호출 시**

```js
const person = {
    name: 'Sam',
    sayHi() {
        console.log(`Hellow~ My name is ${this.name}!`);
        //Hellow~ My name is Sam!
        function innerFunc() {
            console.log(`Hellow~ My name is ${this.name}!`);
            //Hellow~ My name is Sam!
        }
        innerFunc.call(this);
    },
};
person.sayHi();
```

```js
func.call(thisArg, arg1, arg2...)
func.apply(thisArg, [arg1, arg2])
//func.bind(thisArg, arg1, arg2...)
```

**call**과 **apply**는 <u>명시적으로 전달받은 인자를 this에 바인딩해 함수를 실행</u>합니다. 또 **bind**는 <u>함수를 전달한 인자로 this가 바인딩 시켜 복제</u>하는 메서드입니다. 일반 함수에서 window외의 명시적 this를 사용해야 할 때 편리한 메서드이죠.

일반 함수의 this 문제를 마저 이야기 하겠습니다. 일반 함수는 실행시 무조건 global object를 this로 참조합니다. 2번의 코드의 문제는 일반 함수의 안에서 person.name을 this.name으로 사용하고 싶은 경우였습니다. innerFunc을 .call(this)로 실행하면 전달되는 인자가 sayHi의 this인 person이기 때문에 innerFunc의 this도 person에 바인딩 됩니다. 우리가 원했던 대로 출력 내용의 this.name이 Sam으로 변경되었죠!

### 4. 생성자 호출 시

```js
class Person {
    constructor(argName) {
        this.name = argName; // this = 새로 생성하는 인스턴스
    }
    sayHi() {
        console.log(`Hellow~ My name is ${this.name}!`);
        //Hellow~ My name is Sam!
    }
}
let sam = new Person('Sam', 20);
sam.sayHi();
```

js에서 class 함수로 생성된 인스턴스는 <u>constructor를 메서드로 갖고 있는 객체</u>입니다. 2번의 **메서드 호출시 종속 객체를 참조한다**와 같은 규칙을 적용한다고 생각하면 이해하기 쉽습니다. constructor는 인스턴스를 생성하며 자신을 소유하는 this(예시의 sam)을 참조합니다. this.name = name은 객체 sam에 name이라는 속성을 추가하며 파라미터로 전달받은 argName을 할당하겠다는 뜻이죠. sam.sayHi를 실행하면 this.name의 값이 'sam'인 것을 확인 할 수 있습니다.

#### new 연산자 함수 호출 후, 인스턴스를 할당하지 않을시

```js
class Person {
    constructor(argName) {
        this.name = argName;
    }
    sayHi() {
        console.log(this); // Person {name: undefined}
        console.log(`Hellow~ My name is ${this.name}!`);
        // Hellow~ My name is undefined!
    }
}
new Person().sayHi();

console.log(Person); // class Person {constructor(argName){...}
console.log(Person === new Person()); //false
```

추가로 알아보는 new 연산자로 생성했지만 변수 할당은 되지 않은 인스턴스의 this입니다. class는 new 연산자 사용 시 익명 인스턴스를 생성하며 Person class로 생성된 인스턴스라는 의미로 Person{}이 출력됩니다. 실제로는 할당 변수가 없는 객체이기 때문에 Person은 여전히 class를 참조하는 변수로 사용되고 있습니다(6,13,14줄 비교)

### 번외. arrow function

```js
const person = {
    name: 'Sam',
    sayHi() {
        console.log(`Hellow~ My name is ${this.name}!`);
        //Hellow~ My name is Sam!
        innerFunc = () => {
            console.log(`Hellow~ My name is ${this.name}!`);
            //Hellow~ My name is Sam!
        };
        innerFunc(this);
    },
};
person.sayHi();
```

```js
let sam = {
    name: 'sam',
};

(() => {
    console.log(this); //window
}).call(sam);

(function () {
    console.log(this); // sam
}.call(sam));
```

**arrow function**은 es6에 추가된 문법으로 함수를 익명으로 선언하는 또 다른 방법입니다. arrow function은 간결한 표현식 덕분에 재사용 되지 않는 짧은 함수를 표현할 경우 많이 사용되고 있습니다. arrow function의 특징은 모든 함수가 호출 시 자신의 this를 정의하는 것과 달리 **this가 바인딩되지 않는다**는 점입니다. 일반 함수나 metod처럼 this를 참조하지도 않고, call과 apply, bind를 사용해도 명시적 this 바인딩이 불가능합니다. arrow function은 바로 전 context, 즉 **상위 scope의 this를 참조**합니다.

3번 예시의 innerFunc을 arrow function으로 선언하면 innerFunc은 호출되더라도 this를 바인딩하지 않고 상위 스코프인 sayHi의 this를 받기 때문에 출력의 this.name은 person의 Sam이 참조 된 것을 확인 할 수 있습니다.

## 요약

**함수 호출 방식에 따라 this가 참조하는 것들**

-   전역 : global object
-   일반 함수 호출 시 : global object
-   메서드 호출 시 : 종속 object
-   call apply bind : 명시적으로 bind한 object
-   생성자(constructor): 생성된 인스턴스 객체
-   arrow function: 상위 scope의 this

---

**[Posting Reference]**  
[실행 컨텍스트](https://www.zerocho.com/category/JavaScript/post/5741d96d094da4986bc950a0)  
[[JS] 자바스크립트의 The Execution Context (실행 컨텍스트) 와 Hoisting (호이스팅)](https://velog.io/@imacoolgirlyo/JS-%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8%EC%9D%98-Hoisting-The-Execution-Context-%ED%98%B8%EC%9D%B4%EC%8A%A4%ED%8C%85-%EC%8B%A4%ED%96%89-%EC%BB%A8%ED%85%8D%EC%8A%A4%ED%8A%B8-6bjsmmlmgy)  
[실행 컨텍스트와 스코프](https://blog.sonim1.com/135)  
[[JS/this] 자바스크립트, this의 4가지 역할](https://im-developer.tistory.com/96)  
[The JavaScript this Keyword](https://www.w3schools.com/js/js_this.asp)
[Strict mode](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Strict_mode)
