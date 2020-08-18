---
layout: post
title: 'HTML Element를 생성하는 세가지 기술'
subtitle: ' 단순 노동을 줄이고 편히 살고 싶은 노력 '
date: 2020-08-18 09:13:00
author: 'Dev X'
header-img: 'img/post-bg/post-bg-dom.jpg'
published: false
catalog: true
tags:
    - js
    - dom
    - html
    -
---

## HTML Element를 생성하는 노동

js에서 dom을 다루다보면 가장 번잡한 일이 새로운 HTML Element를 만들어 추가하는 것 입니다. 사람마다 다르겠지만 저의 경우엔 js로 HTML Element를 생성하는 코드는 가독성도 떨어지고 단순 노동처럼 여겨졌던 것 같습니다. HTML Element를 생성하는 방법은 여러가지가 있는데요 오늘은 세 가지 방법을 예시로 들며 어느 쪽이 가장 생산성이 높은지 고민해보겠습니다.
<br/><br/><br/>

![목표로하는 HTML 구조 스크린샷](https://i.ibb.co/8Ysgqdp/1.png)

```html
<div class="container">
    <header>
        <h1 class="heading__primary">Create HTML Element</h1>
    </header>
    <ul class="item--container">
        <li class="item">Item 1</li>
        <li class="item">Item 2</li>
        <li class="item">Item 3</li>
        <li class="item">Item 4</li>
    </ul>
</div>
```

<center>위의 구조를 만드는 것을 목표로 세 가지 예시를 들어 살펴보겠습니다.</center>

<br/><br/>

### 1. insertAdjacentHTML

```js
element.insertAdjacentHTML(position, text);
```

```js
const Title = 'Create HTML Element';
const groupOfItems = ['Item 1', 'Item 2', 'Item 3', 'Item 4'];
const makeItems = (groupOfItems) => {
    let htmlStr = '';
    for (let item of groupOfItems) {
        htmlStr += `<li class="item">${item}</li>`;
    }
    return htmlStr;
};

document.querySelector('body').insertAdjacentHTML(
    'beforeend',
    `<div class="container">
        <header>
            <h1 class="heading__primary">${Title}</h1>
        </header>
        <ul class="item--container">
            ${makeItems(groupOfItems)}
        </ul>
    </div>`
);
```

[insertAdjacentHTML](https://developer.mozilla.org/ko/docs/Web/API/Element/insertAdjacentHTML)은 target HTML Element의 특정 위치에 원하는 node를 추가 할 수 있는 메서드입니다. **position**에는 target HTML Element의 앞(beforebegin), 뒤(afterend), 첫번째 자식(afterbegin), 마지막자식(beforeend)의 위치를 지정할 수 있고 **text**는 HTML 또는 XML로 파싱 가능한 String을 받습니다.

**장점**: 가독성이 좋습니다. ES6에서 템플릿 리터럴(``)이 추가된 후로 JSX와 거의 비슷한 형태(만) 갖게 되었습니다. 인자로 text를 받아 HTML 또는 XML로 파싱해주다보니 기존의 HTML을 붙여넣기해 약간만 다듬어주면 작업 시간도 짧은 편입니다.

**단점**: 보안 이슈가 있습니다. insertAdjacentHTML은 text를 HTML로 파싱하며 text에 포함된 script 태그 또한 파싱합니다. 때문에 해커가 웹 페이지에 악성 스크립트를 삽입하는 XSS(Cross-Site Scripting) 공격에 취약합니다. innerHTML의 보안 이슈와 같은 맥락이죠. 이런 공격은 text가 HTML 태그로만 이루어졌는지 필터링 하는 방식으로 막을 수도 있지만, 여전히 다른 방식의 악성 스크립트 공격을 당할 가능성이 있습니다.

<span style="color:grey">저는 코드 가독성을 중요하게 생각하는 편이라 얼마 전까지 insertAdjacentHTML을 애용해 왔습니다. 하지만 보안 이슈가 있다는 것을 알게 되어 다른 HTML Element 생성 방식을 찾던 것이 이 포스팅을 작성한 계기입니다.</span>
<br/><br/>
<br/>

### 2. createElement

```js
let element = document.createElement(tagName);
```

```js
const makeHtmlElement = function (tagName, ...attr) {
    const element = document.createElement(tagName);
    for (let prop of attr) {
        const [key, value] = Object.entries(prop)[0];
        if (key == 'textContent' || key == 'innerText') {
            element.textContent = value;
        } else {
            element.setAttribute(key, value);
        }
    }
    return element;
};

const container = makeHtmlElement('div', { class: 'container' });
const header = makeHtmlElement('header');
const heading = makeHtmlElement(
    'h1',
    { class: 'heading__primary' },
    { textContent: 'Create HTML Element' }
);
const itemContainer = makeHtmlElement('ul', { class: 'item--container' });
const groupOfItems = ['Item 1', 'Item 2', 'Item 3', 'Item 4'];
const [item1, item2, item3, item4] = groupOfItems.map((item) =>
    makeHtmlElement('li', { class: 'item' }, { textContent: item })
);

itemContainer.append(item1, item2, item3, item4);
header.append(heading);
container.append(header, itemContainer);
document.querySelector('body').append(container);
```

createElement는 지정 tagName의 HTML Element를 생성하는 메서드입니다. **tagName**은 생성하려는 element의 타입의 string입니다.

**장점**: HTML Element를 생성해 append하기 때문에 node만 삽입됩니다.  
**단점**: 반복되는 코드를 function을 이용해 줄여보려 했지만 그래도 가독성이 안 좋습니다. 또 HTML element를 생성할때마다 dom tree 자료구조를 탐색해야하기 때문에 js의 성능을 저하시킵니다.

<br/>

### 3. HTML Template & Document Fragment

**HTML**

```html
<template id="temp">
    <div class="container">
        <header>
            <h1 class="heading__primary"></h1>
        </header>
        <ul class="item--container">
            <li class="item"></li>
            <li class="item"></li>
            <li class="item"></li>
            <li class="item"></li>
        </ul>
    </div>
</template>
```

**js**

```js
const element = document.importNode(
    document.querySelector('#temp').content,
    true
);
const copiedNode = element.children.item(0);

copiedNode.querySelector('.heading__primary').textContent =
    'Create HTML Element';
copiedNode.querySelectorAll('.item').forEach((el, i) => {
    el.textContent = `Item ${i}`;
});

document.querySelector('body').append(copiedNode);
```

createElement를 사용할때보다 확연하게 코드 양이 줄었습니다.

<br/><br/><br/>

**[포스팅 레퍼런스]**  
[DocumentFragment노드](https://programmer-seva.tistory.com/60)  
[XSS](https://ko.wikipedia.org/wiki/%EC%82%AC%EC%9D%B4%ED%8A%B8_%EA%B0%84_%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8C%85)  
[DocumentFragment를 이용한 JavaScript 성능 최적화](https://untitledtblog.tistory.com/44)
