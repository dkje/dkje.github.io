---
layout: post
title: "[JS] 상속 뜯어보기"
subtitle: " 상속이라고 쓰고 Prototype Chain이라고 읽는다 "
date: 2020-09-12 19:16:00
author: "Dev X"
header-img: "img/post-bg/post-bg-js.png"
published: true
catalog: true
tags:
    - JS
    - class
    - inheritance
    - prototype
    - prototype chain
---

[Class 뜯어보기](https://dkje.github.io/2020/09/09/Class/)에서 prototype과 class에 대해 알아보았습니다. 이 글은 **상속**을 prototype으로 어떻게 구현하는지에 대해 알아보고 js의 class 상속에 대해 이해하는 목적으로 정리되었습니다.

## 👩‍👦‍👦상속이란

상속은 객체들간의 관계를 구축하는 방법입니다. <u>여러 객체 생성 함수가 같은 속성을 갖고 있다면</u>, 해당 객체는 **상속**으로 관리하는 것이 편리합니다.

![상속 예시](https://i.ibb.co/KGVyMR0/image.png)

예를 들어 Person과 Student, Teacher라는 객체 생성 함수가 name과 age라는 속성과 sayHi()라는 유사성을 갖고 있다고 전제해보겠습니다. 3개의 객체 생성 함수는 각기 다른 인스턴스를 생성하지만 교집합적 속성을 Person 객체 생성 함수에서 갖고 있습니다. 만약 Person이 갖고 있는 공통 속성을 Student와 Teacher에 연결시켜 사용할 수 있다면 관리가 더욱 효율적이겠죠?

이런 객체 관계를 **상속**이라고 부르며, 기능을 제공하는 함수를 **상위 클래스** 기능을 제공받는 함수를 **하위 클래스**라고 합니다. JS에서 상위 클래스와 하위 클래스는 일반적으로 **일대다**의 형태를 이룹니다. OOP에는 다중 상속이라는 개념 또한 존재하지만 ES6는 다중 상속을 지원하지 않기 때문에 이 글은 단일 상속을 전제로 정리되었습니다.

<span style="color:grey">
ES6에서 상속은 extends라는 키워드로 구현됩니다. 개인적으로 '상속'보다 '확장'이라는 개념으로 이해하는 것이, <u>상위 클래스로부터 전달받은 기능을 하위 클래스에서 확장시킨다</u>는 목적이 더 잘 전달 되는것 같습니다.
</span>

## 💾prototype의 상속 구현

#### 1. 상속을 구현하기 전의 모습

```js
const Person = function (name, age) {
    this.name = name;
    this.age = age;
};

Person.prototype.sayHi = function () {
    console.log(`Hi! my name is ${this.name}`);
};

const Student = function (name, age, classMate) {
    this.name = name;
    this.age = age;
    this.classMate = classMate;
};

Student.prototype.sayHi = function () {
    console.log(`Hi! my name is ${this.name}`);
};

Student.prototype.study = function () {
    console.log("I am smart!");
};
```

![상속 구현 전의 uml구조](https://i.ibb.co/mGMFDQL/1.png)

[Pseudo-classical](https://dkje.github.io/2020/09/09/Class/) 방식으로 구현한 Person과 Student 객체 생성 함수입니다. 각각의 함수가 별개로 생성되었기 때문에 메소드 또한 각자의 **prototype**에서 관리되고 있습니다. 우리의 목표는 prototype 속성이 prototype 객체를 참조하는 특성을 이용해, 공통적 메소드(sayHi, constructor)를 Person에게서 Student로 상속시키는 것입니다.

#### 2. 메소드 상속받기

```js
const Student = function (name, age, classMate) {
    this.name = name;
    this.age = age;
    this.classMate = classMate;
};

Student.prototype = Object.create(Person.prototype);

Student.prototype.study = function () {
    console.log("I am smart!");
};

const student = new Student("Peter", 18);
student.sayHi(); //Hi! my name is Peter
student.study(); //I am smart!
```

Student prototype 객체에 <u>'Person prototype 객체를 proto로 갖는 객체'</u>를 Object.create로 생성해 참조시켰습니다. 이제 Person과 Student는 아래와 같은 관계를 갖습니다.

![상속 구현하기 과정 1](https://i.ibb.co/qdS6kT2/3.png)

sayHi 메소드와 study 메소드가 잘 작동하기 때문에 언뜻 보기엔 문제가 없어보입니다. 하지만 Student prototype 객체의 구조를 들여다보면 아직 상속 구현이 미완성 되었다는 것을 알 수 있습니다.

prototype의 **constructor**는 <u>객체 생성 함수</u>를 가리킵니다. Person prototype 객체가 Person 함수를 가리키는 것처럼 말이죠. 하지만 우리는 Student prototype 속성에 Person prototype을 \_\_proto\_\_로 갖는 객체를 새로 할당하며 기존의 constructor를 갖고 있던 prototype 객체와 연결이 끊겼습니다.

```js
const oldPrototype = Student.prototype;
Student.prototype = Object.create(Person.prototype);
console.log(oldPrototype === Student.prototype); ///false
```

Student prototype은 constructor가 없기 때문에 \_\_proto\_\_로 참조 중인 Person의 constructor를 자신의 constructor로 사용할 것입니다. 만약 Student constructor와 Person constructor가 같은 구조를 가졌다면 문제가 없겠지만 Student는 Person에는 없는 classMate라는 속성을 갖고 있습니다. 다음 목표는 **constructor**가 <u>Student 함수를 참조하게 하면서 Person의 constructor를 상속받게</u> 하는 것입니다.

#### 3. constructor 설정하기

```js
const Student = function (name, age, classMate) {
    Person.call(this, name, age);
    this.classMate = classMate;
};

Student.prototype = Object.create(Person.prototype);

Student.prototype.constructor = Student;

Student.prototype.study = function () {
    console.log("I am smart!");
};
```

<u>constructor는 객체 생성 함수를 참조한다</u>는 규칙을 어기지 않으면서, **Student constructor**가 **Person constructor**를 상속받게 하는 방법입니다.

-   8번 줄: Student prototype 객체에 constructor를 추가하며 Student 자신을 할당시킵니다.
-   2번 줄: name과 age를 인자로 Person의 constructor, 즉 Person을 실행합니다.
-   3번 줄: Person constructor의 상속으로 해결되지 않는 나머지 초기 값 할당을 추가합니다.

Person과 Student는 이제 아래와 같은 관계를 갖습니다.

![상속 구현하기 과정 3](https://i.ibb.co/3mfrqFT/7.png)

## 🔗prototype chain

prototype으로 상속을 구현하는 과정에서 우리는 이미 **prototype chain**이라고 불리는 특성을 보았습니다. 객체의 속성에 접근하려 할 때 벌어지는 일을 단계적으로 정리하면 아래와 같습니다.

1. 객체 자신의 **자체 속성**을 확인한다.
2. 만약 해당 속성을 발견하지 못했다면, **prototype 객체**을 확인한다.
3. 만약 해당 속성을 발견하지 못했다면, prototype의 **\_\_proto\_\_**를 확인한다.
4. 속성을 찾았다면 속성을 반환한다.
5. 속성을 못찾았다면 null을 반환한다.

![prototype chain 예시](https://i.ibb.co/PzFMtVf/5.png)

위의 상황을 예시로 들어보겠습니다. Student는 Person을 상속하고 Hight School Student는 Student를 상속하는 관계입니다. 만약 <u>Hight School Student의 인스턴스에서 sayHi 메소드에 접근 할 때</u> 일어나는 일입니다.

1. High School Student의 prototype 객체를 조회한다.
2. sayHi가 없기 때문에 \_\_proto\_\_가 가리키는 Student의 prototype 객체를 조회한다.
3. sayHi가 없기 때문에 \_\_proto\_\_가 가리키는 Person의 prototype 객체를 조회한다.
4. sayHi를 발견해 반환한다.

또 'prototype으로 상속 구현하기'의 3번 'constructor 설정하기'를 떠올려보면 Student의 constructor 속성이 없었을 땐 Person의 prototype을 조회해 constructor를 찾아냈고, Student에 constructor를 추가하고 나서는 Student의 prototype에서 constructor를 찾아내 Person의 prototype까지 가지 않고 해당 속성을 반환했습니다.

---

JS의 <u>모든 객체는 Object를 상속</u> 받습니다. 즉 모든 객체의 \_\_proto\_\_ 속성을 따라가면 그 끝엔 항상 Object가 있다는 뜻이죠.

예를 들면 우리는 모든 객체에서 hasOwnProperty라는 메소드를 사용 할 수 있습니다. hasOwnProperty는 객체가 해당 속성을 갖고 있는지의 여부를 boolean 값으로 반환합니다.

```js
const Person = function (name, age) {
    this.name = name;
    this.age = age;
};

const person = new Person("Peter", 18);
console.log(person.hasOwnProperty("name")); //true
```

Person의 prototype에 hasOwnProperty라는 메소드를 추가하지 않았는데도 사용 할 수 있는 이유는 모든 객체가 Object에 Prototype Link 되어있기 때문입니다. Object prototype의 속성을 살펴보면 hasOwnProperty를 확인할 수 있습니다.

![object의 prototype 객체](https://i.ibb.co/pQB5h0t/6.png)

## 💿class의 상속 구현

class가 prototype의 Syntax Sugar인 것처럼 class의 상속 또한 prototype chain의 Syntax Sugar입니다. prototype chain에 대해 이해한 지식을 바탕으로 ES6의 class 상속 문법을 살펴보겠습니다.

```js
class Person {
    constructor(name, age) {
        this.name = name;
        this.age = age;
    }

    sayHi() {
        console.log(`Hi! my name is ${this.name}`);
    }
}

class Student extends Person {
    constructor(name, age, grade) {
        super(name, age);
        this.job = grade;
    }

    study() {
        console.log("I am smart!");
    }
}
```

-   extends: 상속받을 클래스를 명시합니다. 하위 클래스의 \_\_proto\_\_은 상위 클래스의 prototype 객체를 참조합니다.

-   constructor: prototype에 constructor를 추가합니다. 필수 요소가 아니며 생략할 시엔 상위 클래스의 constructor를 사용하게 됩니다.  
    (생략 시: Student.constructor === Person.constructor //true)

-   super: 상위 클래스의 메서드, 즉 상위 클래스의 prototype을 호출할 때 사용합니다.
    -   super()로 사용했을 때는 상위 클래스의 constructor를 호출합니다.
    -   super.으로 상위 클래스의 메서드에 접근 할 수도 있습니다.
    -   this 키워드보다 먼저 호출해야하며 어길시 참조오류가 발생합니다.

## [Posting Reference]

[MDN 프로토타입 체인을 이용한 상속](https://developer.mozilla.org/ko/docs/Web/JavaScript/Guide/Inheritance_and_the_prototype_chain)  
[상속 (객체 지향 프로그래밍)](<https://ko.wikipedia.org/wiki/%EC%83%81%EC%86%8D_(%EA%B0%9D%EC%B2%B4_%EC%A7%80%ED%96%A5_%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D)>)  
[[Javascript ] 프로토타입 이해하기](https://medium.com/@bluesh55/javascript-prototype-%EC%9D%B4%ED%95%B4%ED%95%98%EA%B8%B0-f8e67c286b67)  
[[생활코딩]super](https://www.opentutorials.org/module/4047/24620)
