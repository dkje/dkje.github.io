---
layout: post
title: '3. 함수'
subtitle: ' "클린 코드 정독 시리즈 3"'
date: 2020-11-07 20:40:00
author: 'Dev X'
header-img: 'img/post-bg/post-bg-cleancode.jpg'
catalog: true
tags:
    - 클린코드
    - js
    - javascript
---

[클린 코드](https://book.naver.com/bookdb/book_detail.nhn?bid=7390287)를 읽고 공부한 내용을 정리한 글입니다.

# Function

#### 😂서문 (나의 흑역사)
```js
function renderPage(){
const root = document.querySelector("#root");
const button = document.createElement("button");
const ul = document.createElement("ul");
button.textContent = "불러오기";

button.addEventListener("click", () => {
  while (ul.firstChild) {
    ul.removeChild(ul.firstChild);
  }

  fetch("https://koreanjson.com/posts")
    .then((response) => response.json())
    .then((data) => {
      for (let post of data) {
        const li = document.createElement("li");
        const id = document.createElement("p");
        id.textContent = post.id;
        const title = document.createElement("p");
        title.textContent = post.title;
        const content = document.createElement("p");
        content.textContent = post.content;
        li.addEventListener("click", () => {
          fetch(`https://koreanjson.com/users/${post.UserId}`)
            .then((response) => response.json())
            .then((data) => {
              window.alert(`${data.name}님이 작성하신 글입니다`);
            })
            .catch((error) => console.log(error));
        });
        li.append(id, title, content);
        ul.append(li);
      }
      root.append(ul);
    })
    .catch((error) => console.log(error));
});

root.append(button);
}
```

처음 코딩을 배우기 시작 했을 때 내가 만든 코드들은 대부분 이런 형식을 하고 있었다. 길고, 중복된 내용도 많으며, 문맥이 두서없고, 뭘 하는지도 모르겠다. 이런 코드들이 가진 강력한 힘은 <u>코드를 읽고 싶은 의욕을 없애버린다는 것이다.</u> 당장은 방금 짠 코드들을 기억하고 있으니 문제가 없겠지만, 나중에 이 코드를 수정하게 된다면 이걸 수정하느니 차라리 다시 짜겠다라는 말이 나올 것이다.

이런 코드를 짜게 되는 이유가 여렀있었는데

- 함수를 어떻게 짜야 할 지 몰랐다
- 계획없이 의식의 흐름대로 코딩했다
- 시간이 없어서 일단 작동하는 코드를 짜야했다

보통 이런 식이다.

사연은 다양하지만 이런 함수들이 가져오는 단점은 확실하다.

- 코드를 이해하는데 오래 걸린다
- 코드를 재사용할 수 없다
- 중복된 코드를 수정하는데 많은 시간이 걸리고, 수정 과정에서 실수를 할 가능성이 높다

그렇다면 함수를 잘 짜기 위해선 어떤 점을 고려해야 할까?


## 1. 최대한 작게 만들어라

함수는 더 이상 줄일 수 없을 때까지 작게 만들어야 한다. 긴 함수의 장점을 말하긴 어렵지만 작은 함수의 장점을 말하기는 쉽다. 우선 코드 **재 사용성**이 높아지고, **가독성**도 좋다. 무엇보다, 함수를 추상화하는 과정에서 각 함수의 **책임이 명백**해진다.  

블록에 중첩 구조가 생기거나, 들여쓰기가 2단을 넘을만큼 큰 함수는 좋지 않다.

if문과 while문은 자칫 중첩 구조가 생기기 쉬운데, 구문 내에서 '이름을 잘 지은' 함수를 호출하는 식으로 함수를 줄여야 한다.

```js
async function initPage(){
  const data = await getData();
  renderPostPage(data);
}
```
서문의 못생겼던 함수를 1차적으로 정리했다.

**1. 함수명을 renderPage에서 initPage 변경했다.**  
함수를 줄이는 과정에서 이 함수가 render와 관련되지 않은 일도 하고 있었기 때문에 이름에 수정이 필요했다.

**2. 코드를 추상화 해 다른 함수로 분리시켰다.**  
view(renderPostPage)와 model(getData)로 역할을 나눠 함수를 분리했고, 함수명을 통해 코드를 쉽게 이해 할 수 있게 되었다.


## 2. 함수는 한 가지 일만 해야한다

자주 들어본 말이지만 <u>한 가지 일만 한다</u>는 말을 이해하기는 어렵다. 바로 위의 initPage 봐도 data를 불러오고, 화면을 렌더링하는 두 가지 일을 한다고 생각 할 수도 있다. 사실 한 가지 일을 하는 함수는 '**한 가지 추상화 단계**의 일만 하는 함수'라고 표현하는 것이 더 이해하기 쉽다.

### 추상화 수준

![추상화 수준 예시](https://i.ibb.co/ZfMPpVB/image.gif)
<center>구글 어스를 확대 할 수록 추상화 수준이 낮아지고 있다</center>

객체 지향을 공부 할 때 가장 먼저 추상화라는 개념을 배운다. 추상화는 '**공통적인 특성을 한데 모아 그룹으로 표현한 것**'이다. 그럼 **추상화 수준**이란 뭘까? 먼저 현실의 상황을 예로 들어보자. 학생 A,B는 한 가지 추상화 수준의 대화를 하고 있고, C는 추상화 수준이 뒤섞인 대화를 하고 있다.

> 학생 A : 어젯 밤에 **화성**과 **금성**, **토성**을 보았어. 정말 아름다웠어.  
> 학생 B : 방학에 비행기를 탔는데 **인천**, **서울**, **대전**이 다 보이더라. 정말 신기했어.  
> 학생 C : 나도 **화성**을 정말 좋아해. 그리고 **화성시**도 살기 좋은 곳이더라.

추상화가 공통적인 특성을 그룹으로 묶어 말하는 것이라면 **추상화 수준**은 '**추상화된 정도**'이다. 화성, 금성, 토성과 같은 행성을 '**높은 추상화 수준**'으로 묶인 그룹이라고 한다면, 서울, 인천, 대전은 상대적으로 '**낮은 추상화 수준**'으로 묶인 그룹이다. 학생 A와 B를 함수로 생각한다면 **한 가지 추상 단계**에서, 즉 **한 가지 일**을 한다고 말할 수 있다.

이렇게 함수가 한가지 추상 수준의 일만 하도록 설계하는 것을 **단일 추상화 원리(SLAP)**라고 한다. 단일 추상화 원리에 의해 짜여진 함수는 깊이 들어갈수록 추상화 수준이 낮아진다.

![함수 추상화의 예시](https://i.ibb.co/HKFPzvS/image.png)

단일 추상 원리에 따라 짜여진 함수의 장점은 '**이야기처럼 읽기 쉽다**'는 것이다. 다른 말로는 **내려가기 규칙**이라고도 하는데, 코드를 위에서 아래로 읽으면, 추상화 수준이 점차 낮아지는 흐름을 따라 개념의 세부 사항을 이해하기 쉽다.

또 다른 장점은 시스템의 **유연성**이 높아진다는 것이다. 추상화 수준에 따라 함수를 쪼개는 과정에서, 가장 세분화된 함수는 단일 책임을 갖고 모듈화되기 때문에 시스템의 확장성이 높아진다.

**내 함수가 한가지 일을 하는지 확인하기**  
그래도 추상성은 개념적이기 때문에 내가 짠 함수가 한가지 추상 단계인지를 확신하기 어려울 수 있다. 그 땐 아래의 관점에서 생각해보자.

- **이름 추출하기**: 의미있는 이름으로 다른 함수를 추출 할 수 있다면 한 가지 일을 하는 함수가 아니다. 
- **섹션 나누기**: 코드를 여러 섹션으로 나눌 수 있다면, 한 함수가 여러가지 일을 하고 있다.


## 3. 서술적인 이름을 사용해라
긴 이름은 코드를 길게 만들지만, 짧고 부정확한 이름보다는 백배 낫다. 함수의 이름은 함수가 하는 일을 **정확하게** 표현해야 한다. 억지로 이름을 짧게 지으려다보면 이름의 전달력이 떨어지고, 주석으로 함수가 하는 일을 설명하게 된다. 이름을 짓는 과정에서 함수가 하는 일이 명확해지고, 만약 함수가 여러 일을 하고 있다면 분리 할 수도 있을 것이다. 함수 이름 짓기에는 충분한 시간을 들이는 것이 좋다.

## 4. 인수는 적을수록 좋다
인수는 함수를 이해하기 어렵게 만든다. 함수 내에서 인수가 무엇인지 확인 할 수 없기 때문에 함수를 이해하기 위해서는 호출부까지 이동해야 한다. 또 인수는 많을 수록 테스트 부담을 늘인다. 유효한 인수의 조합만큼 함수를 검증해야하기 때문에 고려할 케이스 수가 늘어난다.

### 단항 함수
흔히 사용되는 단항 함수의 예로는 **함수에 질문을 던지는 경우**이다. isValidEmail('test@abc.com')이라는 함수는 문자열을 인자로 받아 검증한 결과를 boolean으로 반환하는 함수일 것이다. 다른 단항 함수의 예로는 **인수를 변환해 결과를 반환**하는 경우이다. 예를 들면 queryselector('div')의 경우인데, 인자에 맞는 HTML Element를 찾아 반환한다. 이런 상황이 아니라면 단항 함수도 가급적 피해야 한다. 

### 플래그 인수
플래그를 인자로 사용하는 것은, 결국 여러가지 일을 처리하는 것이다. 함수가 하는 일을 분리해 여러 함수로 나눌 수는 없는지 고려해야 한다.

### 이항 함수
이항 함수는 더욱 혼란스럽다. 인자가 함수에 미치는 영향을 추측하기 어렵기 때문에, 읽는데 시간이 더 걸리고 이해하기 어렵다. 또 인자의 순서가 읽는 사람을 혼란스럽게 만든다. 예를 들면 assertEqual 함수의 인자는 (expected,actual) 순서인지 (actual,expected)인지 알 수 있는 힌트가 없다. 이 경우에는 함수 명을 변경하는 것도 한 방법이다. assertExpectedEqualsActual이라는 이름이었다면 인자의 순서가 자연스럽게 전달될 것이다.

이항 형식이 적절한 경우도 있다. 평면 좌표를 반환하는 point함수가 있다면 (x,y) 인자를 받는 것이 자연스럽다. 또 위의 예시와 달리 인자의 순서 또한 자연적으로 인식되기 때문에 이항을 사용해도 문제가 없다.

### 삼항 함수
인자가 3개를 넘어간다면, 인자의 일부라도 변수로 묶을 수 있는 지 고민해보자. createProfile('name',25,'korea')라는 함수가 있다면 3개의 인자를 묶어 createProfile(userProfileData)라는 인자로 전달 할 수 있을 것이다. 변수화 시키는 과정에서 개념을 표현하기 때문에 함수를 이해하기 수월해진다.

## 5. 명령과 조회를 분리해라
```js
function set(key, newValue){
  try{
    const user = Store.find(key);
    if(user) Store.update({key}, {value:newValue});
  }catch(error){
    return false;
  }
  return true;
}
```
set이라는 함수가 있다고 하자. 이 함수는 User에 해당 이름을 가진 user가 있는지 확인 후, 만약 존재한다면 username을 newName으로 변경한 뒤 성공 여부를 반환한다. 이런 함수를 아래와 같이 사용한다고 하자.

```js
if(set("Kim","Song")) doSomethig();
```

첫째로 set이라는 함수의 이름이 무엇을 의미하는지 불명확해졌다. 애초에 set이라는 이름이 갖고있는 의미만으론 무엇을 return하는지 알 수 없다. if 문의 조건을 정확하게 알 수 없으므로 아래와 같이 분리해서 사용하는 것이 직관적이다.

```js
if(attributeExists("Kim")){
  setStore("Kim","Song")
}
```


---

## [REFERENCE]
[추상화 수준을 결정하는 방법](https://qastack.kr/software/110933/how-to-determine-the-levels-of-abstraction)  
[[CleanCode] 함수의 추상화 수준이란?](https://sosimhan-dev.tistory.com/4)  
[Levels of Abstraction, A Key Concept in Systems Design](https://medium.com/@danieljyoo/levels-of-abstraction-a-key-concept-in-systems-design-7fdb33d288af)  

\[클린 코드 정독 시리즈\]  
[1. 어떤 개발자가 돼야 할까?](https://dkje.github.io/2020/07/29/CleanCodeSeries1/)  
[2. 변수명과 함수명 짓기](https://dkje.github.io/2020/08/03/CleanCodeSeries2/)


