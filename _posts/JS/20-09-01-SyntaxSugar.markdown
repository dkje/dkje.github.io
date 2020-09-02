---
layout: post
title: "[JS]Syntax Sugar(문법 설탕)"
subtitle: " 자스인듯~ 자스아닌듯~ 자스같은 너~ "
date: 2020-09-01 23:16:00
author: "Dev X"
header-img: "img/post-bg/post-bg-js.png"
published: true
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

### Declaring Variables Shorthand(변수 단축 선언)

```js
let a,
    b = 1,
    c;
//a : undefined
//b : 2
//c : undefined

const d = 4,
    e = 5;
//const d,e=5; => Uncaught SyntaxError: Missing initializer in const declaration
```

같은 선언 타입을 가진 변수를 한 줄로 선언하는 방법입니다. const로 단축 선언 시 초기값 할당을 하지 않으면 SyntaxError가 발생한다는 점을 유의해야 합니다.

### Short-circuit Evaluation(단축 평가 값)

```js
const str = "some text";
const result1 = str || "default value";
// result1 = "some text";

const nothing = null;
const result2 = nothing || "default value";
// result2 = "default value";

const result3 = str && "If str is truthy return this";
//result3 = "If str is truthy return this"
```

논리 연산자가 왼쪽에서 오른쪽으로 평가를 실행하며, 더 이상 <u>평가가 필요하지 않을 시 실행을 중단</u>하는 것을 단축 평가라 합니다. 논리 연산자는 평가가 종료되는 기점의 값을 리턴하는 특징갖고 있으며, 이 특징 덕에 if문 외에도 논리 연산자를 유용하게 사용할 수 있습니다.

result1에서는 str이 truthy값이기 때문에 OR 연산자가 더 이상 평가를 할 필요가 없다는 판단을 마치고 str의 값을 return했습니다. 반대로 result2에서는 nothing이 falsy 값이었기 때문에 OR 연산자는 그 다음 'default value'가 truthy 값인 것을 확인 후 return해주었습니다.

result3에서는 str이 truthy값인 것을 확인 후 AND 연산자가 다음의 값을 추가로 평가합니다. "If str is truthy return this" 또한 truthy값이기 때문에 마지막 평가 값인 "If str is truthy return this"를 return했습니다.

### Nullish coalescing operator(널 병합 연산자)

```js
let foo = 0;
const result1 = foo || "default Value"; //"default Value"
const result2 = foo ?? "default Value"; //0
```

널 병합 연산자는 왼쪽 표현식이 **null, undefined**인지의 값을 확인합니다. AND와 OR연산자가 truthy와 falsy값을 확인하는 것과 달리 null, undefined만 false로 구분시키는 것이 특징입니다.

### Ternary Operator(삼항 조건 연산자)

```js
condition ? exprIfTrue : exprIfFalse;
```

[삼항 조건 연산자](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Operators/Conditional_Operator)는 if문의 단축 형태입니다. condition을 Truthy와 Falsy 한 값으로 구분해 true일 경우 exprIfTrue을 실행한 값을 return하고 false일 경우 exprIfFalse를 실행한 값을 return합니다.

```js
if (true) {
    return "a";
}

true ? "a" : null;
```

if문과 차이가 있다면 반드시 삼항에 해당하는 코드가 모두 작성되어야 한다는 것입니다. exprIfTrue나 exprIfFalse에 해당하는 실행문 중 하나라도 누락되면 SyntaxError가 발생합니다. True에만 작동하는 삼항 연산자를 작성하고 싶다면 위의 코드처럼 return 값으로 null을 작성해야 합니다.

```js
let isMorning = false;
const name = "Peter";

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
const name = "Peter";

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
const maybeName = "Peter";

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

### Property shorthand(단축 속성명)

```js
const a = 1,
    b = 2,
    c = 3;

const obj1 = {
    a: a,
    b: b,
    c: c,
};

const obj2 = { a, b, c }; // {a:1,b:2,c:3}
```

객체에 속성으로 사용하려는 키 값과 같은 이름의 변수가 있을 경우, key : value 할당을 축약형으로 사용 할 수 있습니다.

### Spread syntax(전개 구문)

```js
[...iterableObj, "a", "b", "c"];
let objClone = { ...obj };
myFunction(...iterableObj);
```

object spread라고도 합니다. object앞에 '...' 연산자를 붙이면 object의 literal을 복제하는 기능이며, 객체 특성에 따라 다른 상황에 사용 할 수 있습니다. 예시와 함께 보겠습니다.

```js
[..."Hello"];
//["H", "e", "l", "l", "o"]
```

전개 구문은 보통 배열, 객체에 많이 사용을 하지만 [iterable](https://helloworldjavascript.net/pages/260-iteration.html)한 객체는 모두 사용이 가능합니다. String 또한 **Iterable** 객체이기 때문에 전개 연산자 사용이 가능합니다. 위의 코드에선 String의 char 하나씩을 요소로 받아 새로운 배열의 초기값으로 사용했습니다.

```js
const abcArr = ["a", "b", "c"];
const defArr = ["d", "e", "f"];
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
    name: "dudu",
    age: 2,
    leg: 4,
    fur: true,
    hobby: "sleep",
};

const snake = {
    name: "cucu",
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

객체 리터럴에도 전개구문이 사용 가능합니다. 기존엔 객체를 복사할때 assign 함수를 사용해 빈 객체에 복제했습니다. 하지만 전개 구문을 사용하면 빈객체에 전개구문으로 객체 리터럴을 복제하는 더 직관적 표현이 가능합니다. 객체를 합쳐 새로운 객체를 만드는 것도 전개 구문을 사용하면 더 짧은 구문으로 표현 할 수 있습니다. 중복 키 값이 있다면 뒤에 추가되는 객체 값으로 덮어씌워지는 것을 유의해야 합니다.

### Rest Parameter(나머지 매개변수)

```js
function fn1(name, age, ...arg) {
    // name = 'Dick'
    // age = 21
    // arg = ['handsome','cool','nice'];
}
fn1("Dick", 21, "handsome", "cool", "nice");
```

Rest Parameter는 매개변수의 이름 앞에 ...을 붙여, **할당되지 않은 매개변수를 배열로 받는 기능**을 합니다. parameter는 arguments의 순서에 따라 할당되기 때문에 가장 마지막 parameter에만 사용이 가능합니다. 만약 fn1(...arg,name)과 같이 Rest Parameter 뒤에 다른 Parameter가 선언되었을 경우 SyntaxError가 발생합니다.

### Default Parameter Values(기본값 매개변수)

```js
function fn1(name = "John Doe", age = 30) {
    console.log(`${name} is ${age} years old`);
}
fn1();
//John Doe is 30 years old
fn1("Peter", 15);
//Peter is 15 years old
```

기본값 매개변수는 argument 없이 함수가 실행될 경우 undefined 대신 사용 될 값을 할당하는 방식입니다. 함수 내부에서 인자 값이 전달되었는지 확인하는 절차를 줄여줄 수 있는 유용한 기능입니다.

### Template Literals(템플릿 리터럴)

```js
const hi = "Good Morning";
console.log(`${hi}`); //Good Morning
console.log(`${3 + 6}`); //9
console.log(`${hi ? hi : "Good Evening"}`); //Good Morning
```

템플릿 리터럴은 표현식을 허용하는 문자열 리터럴입니다. 작은 따음표 대신 백틱을 이용하며, \${} 표기법안에 표현식을 사용하는 것이 가능한 유용한 기능입니다.

### String to Number

```js
parseFloat("100.1") + //100.1
    "100.1"; //100.1
parseFloat("100.1") === +"100.1"; //true
```

Js의 암시적 형변환을 이용해 String을 Number로 변환하는 방법입니다. 산술 연산자 +는 String을 이어붙이는데 사용 할 수도 있지만 **숫자의 계산을 우선**적으로 하기 때문에 피연산자의 값이 숫자형일 경우 **Number값을 반환**합니다.

### Double Bitwise NOT(이중 비트연산자 NOT)

```js
Math.floor(4.9); //4
~~4.9; //4
Math.floor(4.9) === ~~4.9; //true
```

```js
~~null; // => 0
~~undefined; // => 0
~~""; // => 0
~~0; // => 0
~~{}; // => 0
~~[]; // => 0
~~(1 / 0); // => 0
~~false; // => 0
~~true; // => 1
~~1.2543; // => 1
~~4.9; // => 4
~~-2.999; // => -2
```

js에서 [Bitwise NOT 연산자](https://j11y.io/cool-stuff/double-bitwise-not/)(~)를 사용하면 <u>소수점 뒤의 값을 무시하고</u> 32진수 비트 값을 반전하는 것을 이용해 Round처럼 사용하는 방법입니다.

1. ~~의 앞 ~은 피연산자 소숫점 아래 값을 제거하고 32진수의 반전된 비트 값을 반환합니다.
2. ~~의 뒤 ~은 비트를 다시 반전해 10진수로 반환합니다.

또 이중 비트 NOT 연산자는 숫자로 변환 가능한 값은 암묵적 형변환 값이 반환되고, 그 외의 값은 truthy false를 판단해 0과 1을 반환합니다.

### Object Destructuring(구조 분해 할당)

[구조 분해 할당](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment)은 배열이나 객체의 속성을 해체하여 그 값을 개별 변수에 담을 수 있게 하는 표현식입니다.

```js
let [a, b] = ["1", "2", "3"];
// a : 1 , b : 2
let [c, d, ...e] = ["1", "2", "3", "4", "5"];
// c : 1, d : 2, e : [3, 4, 5]
```

배열에서 구조 분해 할당을 사용할 시엔 새로 선언하는 객체의 인데스와 같은 위치의 요소 하나를 값으로 받습니다. 구조 분해 할당은 전개 구문과 함께 사용 할 수도 있습니다. 변수 명이 지정된 요소(c,d)는 해당 변수에 할당이 되고, 이어지는 나머지 요소들은 spread로 지정된 변수(e)에 배열로 할당됩니다.

```js
let { apple, banana, carrot, ...fruits } = {
    kiwi: 4,
    apple: 5,
    banana: 3,
    watermelon: 7,
};
// apple: 5 , banana: 3 , carrot:undefined, fruits: {kiwi: 4, watermelon: 7}
```

객체에서 구조 분해 할당을 사용할 때는 선언하는 변수와 같은 이름의 키를 찾아 그 값을 받습니다. 만약 해당 키값이 없다면 undefined가 할당 됩니다.

```js
let o = { p: 42, q: true };
let { p: foo, q: bar } = o;
// foo = 42 , bar = true
```

객체의 구조 분해는 속성을 해체해 다른 변수에 할당하는 것도 가능합니다. key : value와 비슷한 형태의 구문이라 혼란스러울 수 있겠지만, p의 value 값을 해체해 새로 선언하는 변수 foo에 할당하는 기능을 합니다.

## [Posting Reference]

[Syntactic Sugar and JavaScript Diabetes](https://www.freecodecamp.org/news/js-diabetes-and-understanding-syntax-sugar-5de249ee9ebc/)  
[[javascript] ES6 rest 파라미터와 Spread Syntax(전개 구문)](https://mber.tistory.com/14)  
[구조분해할당](https://ko.javascript.info/destructuring-assignment)  
[단축 평가 값 (Short-circuit Evaluation)](http://milooy.github.io/TIL/JavaScript/short-circuit.html)  
[Double bitwise NOT (~~)](https://j11y.io/cool-stuff/double-bitwise-not/)
