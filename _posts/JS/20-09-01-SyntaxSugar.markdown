---
layout: post
title: '[JS]Syntax Sugar(문법 설탕)'
subtitle: ' 자스인듯~ 자스아닌듯~ 자스같은 너~ '
date: 2020-09-01 23:16:00
author: 'Dev X'
header-img: 'img/post-bg/post-bg-js.png'
published: false
catalog: true
tags:
    - js
    - syntax sugar
---

![syntax sugar meme](https://i.ibb.co/cDYP3f2/syntactic-sugar.jpg)

js 기초 갓 떼고나서 다른 사람의 레퍼런스를 참조할때 분명 같은 언어를 사용하고 있는데도 낯선 문법을 만난 적이 있습니다. _이게 뭐지? 이것도 자바스크립트인가...?_ 싶으면서 <u>묘하게 의미를 이해할 수 있는</u> 신기한 코드들을 보고 이런 코드를 뭐라고 하는 걸까? 라는 호기심이 생겨 syntax sugar에 대해 정리해보았습니다.

## Syntax Sugar이란?

Syntax Sugar는 한국어로 문법 설탕이라고 번역됩니다. JS뿐만 아니라 프로그래밍 언어 전반적으로 적용되는 개념이며, 달달한 이름에 걸맞게 <u>읽는 사람 또는 작성하는 사람이 편하게 디자인 된 문법<u>이라는 뜻을 갖고 있습니다.

Syntax Sugar는 직관적이고 간결한 문법을 갖고 있습니다. 번거롭게 작성해야 했던 코드가 짧아진 덕분에 가독성이 좋아지는 효과가 있습니다. 모든 상황에 Syntax Sugar를 적용하는 것이 옳지는 않지만, 직관적으로 작성자의 의도를 알 수 있는 코드라면 Syntax Sugar를 사용하는 것이 생산성을 높여줄 것입니다.

---

### Ternary Operator(삼항 조건 연산자)

```js
condition ? exprIfTrue : exprIfFalse;
```

[삼항 조건 연산자](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Operators/Conditional_Operator)는 if문의 단축 형태입니다. condition을 Truthy와 Falsy 한 값으로 구분해 true일 경우 exprIfTrue을 실행한 값을 return하고 false일 경우 exprIfFalse를 실행한 값을 return합니다.

```js
if (true) {
    return 'a';
}

true ? 'a' : null;
```

if문과 차이가 있다면 반드시 삼항에 해당하는 코드가 모두 작성되어야 한다는 것입니다. exprIfTrue나 exprIfFalse에 해당하는 실행문 중 하나라도 누락되면 SyntaxError가 발생합니다. True에만 작동하는 삼항 연산자를 작성하고 싶다면 위의 코드처럼 return 값으로 null을 작성해야 합니다.

```js
let isMorning = false;
const name = 'Peter';

if (isMorning) {
    console.log(`Good Morning! ${name}`);
} else {
    console.log(`Hi! ${name}`);
}

console.log(isMorning ? `Good Morning ${name}` : `Hi! ${name}`);
```

기본적 삼항 조건 연산자의 사용 예입니다. if 문으로 작성했을 때는 4줄이었던 코드가 1줄로 줄어들고 표현식이 한눈에 들어와 가독성이 좋아졌습니다.

```js
let isMorning = false;
const name = 'Peter';

if (isMorning) {
    console.log(`Good Morning! ${name}`);
    isMorning = false;
} else {
    console.log(`Hi! ${name}`);
    isMorning = true;
}

isMorning
    ? (console.log(`Good Morning! ${name}`), (isMorning = false))
    : (console.log(`Hi! ${name}`), (isMorning = true));
```

조건 실행문이 여러 줄인 경우를 삼항 조건 연산자로 작성해보았습니다. exprIfTrue와 exprIfFalse에 해당하는 코드를 소괄호로 묶고 실행 단위를 ,로 구분해주었습니다. expr를 묶는 소괄호가 없다면 SyntaxError가 발생하는 점을 유의해야 합니다.

```js
let isMorning = false;
const maybeName = 'Peter';

if (isMorning) {
    if (maybeName) {
        console.log(`Good Morning! ${maybeName}`);
    } else {
        console.log(`Good Morning!`);
    }
} else {
    if (maybeName) {
        console.log(`Hi! ${maybeName}`);
    } else {
        console.log(`Hi!`);
    }
}

isMorning
    ? maybeName
        ? console.log(`Good Morning! ${maybeName}`)
        : console.log(`Good Morning!`)
    : maybeName
    ? console.log(`Hi! ${maybeName}`)
    : console.log(`Hi!`);
```

```js
let isMorning = false;
let isAfternoon = false;
let isNight = true;

if (isMorning) {
    console.log(`Good Morning!`);
} else if (isAfternoon) {
    console.log(`Good Afternoon!`);
} else if (isNight) {
    console.log(`Good Night!`);
} else {
    console.log(`Hello!`);
}

isMorning
    ? console.log(`Good Morning!`)
    : isAfternoon
    ? console.log(`Good Afternoon!`)
    : isNight
    ? console.log(`Good Night!`)
    : console.log(`Hello!`);
```

중첩 if문과 else if문도 삼항 조건 연산자로 작성하는 것이 가능합니다. exprIfTrue와 exprIfTrue에 해당하는 구문에 다시 삼항 조건 연산문을 작성하는 원리입니다.

개인적으로 nested된 삼항 조건 연산자는 가독성이 오히려 떨어져, 한줄로 표현 가능한 경우에만 사용하게 되는 것 같습니다. 짧은 조건 return문이나, 조건 변수 할당을 해야 할 때 자주 쓰는 편입니다.

### Spread syntax(전개 구문)

```js
[...iterableObj, 'a', 'b', 'c'];
let objClone = { ...obj };
myFunction(...iterableObj);
```

object spread라고도 합니다. object앞에 '...' 연산자를 붙이면 object의 literal을 복제하는 기능이며, 객체 특성에 따라 다른 상황에 사용 할 수 있습니다. 예시와 함께 보겠습니다.

```js
[...'Hello'];
//["H", "e", "l", "l", "o"]
```

전개 구문은 보통 배열, 객체에 많이 사용을 하지만 [iterable](https://helloworldjavascript.net/pages/260-iteration.html)한 객체는 모두 사용이 가능합니다. String 또한 iterable 객체이기 때문에 전개 연산자 사용이 가능합니다. 위의 코드에선 String의 인덱스 단위인 char를 요소 하나로 받아 새로운 배열의 초기값으로 사용했습니다.

```js
const abcArr = ['a', 'b', 'c'];
const defArr = ['d', 'e', 'f'];
const newArr1 = abcArr.slice();
const newArr2 = [...abcArr];
const abcdefArr1 = abcArr.concat(defArr).concat(newArr1);
const abcdefArr2 = [...abcArr, ...defArr, ...newArr1];
```

대표적 Iterable 객체인 Array 또한 전개구문을 사용 할 수 있습니다. 배열 앞에 ...를 붙이면 배열 리터럴을 복사해 사용할 수 있습니다. 가장 흔히 사용하는 예시는 배열 원본에 변경없이 복제하거나, 새로운 배열의 요소로 사용하는 경우입니다.

Array의 method를 이용해 배열을 복제할때는 slice로 배열 전체를 잘라내 붙여넣는 식으로 사용했었습니다. 직관적이진 않았지만 널리 사용되던 방법입니다. 하지만 전개 구문을 사용하면 새로운 배열 리터럴에 abcArr의 리터럴을 복제하는 것으로 새 배열을 만들수 있습니다.

배열을 이어붙이는 경우도 같습니다. 기존엔 concat을 두 번 사용해 배열을 이어 붙였어야 했다면 전개연산자는 새로운 배열 리터럴에 기존 배열 리터럴을 추가하는 식으로 더 직관적 표현이 가능합니다.

```js
const dog = {
    name: 'dudu',
    age: 2,
    leg: 4,
    fur: true,
    hobby: 'sleep',
};

const snake = {
    name: 'cucu',
    age: 8,
    leg: 0,
    fur: false,
};
const newDog1 = Object.assign({}, dog);
const newDog2 = { ...dog };
const monster1 = Object.assign(dog, snake);
const monster2 = { ...dog, ...snake };
//{name: "cucu", age: 8, leg: 0, fur: false, hobby: "sleep"}
```

객체 리터럴에도 전개구문이 사용 가능합니다. 기존엔 객체를 복사할때 assign 함수를 사용해 빈 객체에 복제했습니다. 하지만 전개 구문을 사용하면 빈객체에 전개구문으로 객체 리터럴을 복제하는 더 직관적 표현으로 가능합니다. 객체를 합쳐 새로운 객체를 만드는 것도 전개 구문을 사용하면 더 짧은 구문으로 표현 할 수 있습니다. 중복 키 값이 있다면 뒤에 추가되는 객체 값으로 덮어씌워지는 것을 유의해야 합니다.

### Object Destructuring(구조 분해 할당)

[구조 분해 할당](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment)은 배열이나 객체의 속성을 해체하여 그 값을 개별 변수에 담을 수 있게 하는 표현식입니다.

```js
let [a, b] = ['1', '2', '3'];
// a : 1 , b : 2
let [c, d, ...e] = ['1', '2', '3', '4', '5'];
// c : 1, d : 2, e : [3, 4, 5]
```

배열에서 구조 분해 할당을 사용할 시엔 새로 선언하는 객체의 인데스와 같은 위치의 요소 하나를 값으로 받습니다. 구조 분해 할당은 전개 구문과 함께 사용 할 수도 있습니다.

```js
let { apple, banana, ...fruits } = {
    kiwi: 4,
    apple: 5,
    banana: 3,
    watermelon: 7,
};
// apple: 5 , banana: 3 , fruits: {kiwi: 4, watermelon: 7}
```

객체에서 구조 분해 할당을 사용할 때는 선언하는 변수와 값은 이름의 키를 찾아 그 값을 받습니다.

```js
function fn1({ apple }) {
    console.log(apple); //사과
}
let fruits = {
    banana: '바나나',
    apple: '사과',
    watermelon: '수박',
};
fn1(fruits);
```

### Object Rest

## [Posting Reference]

[Syntactic Sugar and JavaScript Diabetes](https://www.freecodecamp.org/news/js-diabetes-and-understanding-syntax-sugar-5de249ee9ebc/)  
[[javascript] ES6 rest 파라미터와 Spread Syntax(전개 구문)](https://mber.tistory.com/14)
