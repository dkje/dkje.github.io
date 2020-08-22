---
layout: post
title: '[JS]Html Element를 생성하는 세가지 방법'
subtitle: ' 단순 노동을 줄이고 편히 살고 싶다 '
date: 2020-08-18 09:13:00
author: 'Dev X'
header-img: 'img/post-bg/post-bg-dom.jpg'
published: true
catalog: true
tags:
    - js
    - dom
    - html
    - documentFragment
    - Node
---

## Html Element를 생성하는 노동

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
<br/><br/><br/>

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

**장점**: HTML Element를 생성해 append하기 때문에 node만 삽입이 가능합니다.  
**단점**: 반복되는 코드를 function을 이용해 줄여보려 했지만 그래도 가독성이 안 좋습니다. 또 HTML element를 생성할때마다 dom tree 자료구조를 탐색해야하기 때문에 js의 성능을 저하시킵니다.

<br/>

### 3. Document Fragment

**HTML**

```html
<template id="temp">
    <div class="container">
        <header>
            <h1 class="heading__primary"></h1>
        </header>
        <ul class="item--container">
            <li class="item"></li>
        </ul>
    </div>
</template>
<!-- clone용도의 template tag -->
```

#### HTML template

template tag는 js를 사용해 인스턴스를 사용할 목적의 HTML 코드를 담는 요소입니다. template tag의 하위 요소들은 DOM에는 존재하나 화면에는 렌더링 되지 않습니다.

**js**

```js
const copiedNode = document.importNode(
    document.querySelector('#temp').content,
    true
); // HTML의 Template 태그를 복사해서 document fragment로 받는다

copiedNode.querySelector('.heading__primary').textContent =
    'Create HTML Element';

const itemContainer = copiedNode.querySelector('.item--container');
const groupOfItems = ['Item 1', 'Item 2', 'Item 3', 'Item 4'];
const item = copiedNode.querySelector('.item').cloneNode(true);

while (itemContainer.firstChild) {
    itemContainer.removeChild(itemContainer.firstChild);
} //item을 추가하기 전 dummy item 삭제

for (let prop of groupOfItems) {
    copieditem = item.cloneNode(true);
    copieditem.textContent = prop;
    itemContainer.append(copieditem);
}

document.querySelector('body').append(copiedNode);
```

#### Document Fragment

우선 짚고 넘어가자면 위의 코드는 HTMLElement를 생성하는 것이 아니라 복사하는 것입니다. HTML template를 복제해 사용하는 방식은 여러가지가 있습니다. querySelector로 사용 할 html element를 선택한 후 변경하고 싶은 속성을 replace해 append하는 방식도 있죠. 왜 그 방식을 사용하지 않고 **importNode**를 사용했는지를 알기 위해선 document와 documentFragment의 차이를 이해해야 합니다. 그 외에 DOM의 개념도 함께 짚고 넘어가겠습니다.

**DOM**  
서버에서 HTML 문서를 전달받은 브라우저는 js가 문서에 접근 할 수 있도록 **[DOM](https://developer.mozilla.org/ko/docs/Glossary/DOM)**이라는 인터페이스를 제공합니다. HTML이 단순히 문서의 구조를 표현한다면, DOM은 HTML로 작성된 웹페이지를 객체로 만들어 표현하고, 저장하고, 조작하는 메서드를 갖고 있습니다. document와 element, window등이 우리가 평소 사용하던 DOM의 object들입니다.

**document**  
**[document](https://developer.mozilla.org/ko/docs/Web/API/Document)**는 DOM의 인터페이스 기능 중 하나로 DOM의 진입점 역할을 수행합니다. window와 document를 혼동하실 수 있는데 **window**는 우리가 사용하는 **브라우저**와 같고 **document**는 문서의 **root document**, HTML의 경우엔 <html>요소를 뜻합니다. HTML 문서의 document는 문서의 header와 body 내의 요소를 포함하고, 문서의 URL과 정의 등의 정보 또한 갖고 있습니다.

**Document Fragment**  
document를 수정할때마다 브라우저에선 **reflow**라는 프로세스를 실행해 문서를 재렌더링합니다. 우리가 append를 남발하는 것이 웹 어플리케이션의 성능 저하를 일으키는 이유죠. 브라우저는 최적화를 위해 **[Document Fragment](https://developer.mozilla.org/en-US/docs/Web/API/DocumentFragment)**라는 인터페이스를 제공합니다. Document Fragment는 document tree 외부에 만들어진 document의 경량화 버젼입니다. document같이 Node와 ParentsNode를 상속받기 때문에 노드의 수정과 삭제가 가능하지만, 문서 외부에 있기 때문에 reflow에 영향을 끼치지 않습니다. 아무리 많은 수정이 이루어져도 마지막에 DocumentFragment를 document tree에 추가하는 단 한 번의 reflow만 일어나는 것이죠.

사용법은 HTML Element와 유사합니다.

```js
let fragment0 = new DocumentFragment();
// 빈 Document Fragment 생성
let fragment1 = document.importNode('htmlElement.content', true);
// HTML 요소를 DocumentFragment로 복사, true일시 자식 요소까지 복사
let element = fragment1.querySelector('tagname');
element.textContent = 'test';
//fragment1의 자식 요소 선택 후 수정
parentElement.append(fragment1);
//document tree에 Document Fragment 추가
```

이렇게 DocumentFragment를 생성 후 원하는 document tree의 요소에 append하고나면 Document Fragment는 갖고 있던 Child Node들을 Parent Node에 추가하고 껍데기만 남게됩니다. createElement로 생성한 element들이 append 한 후에도 메모리에 남아있는 것과 반대이죠.

**장점**: 성능 최적화에 적합합니다. dom의 node가 적을때는 유의미한 차이가 없겠지만 추가 할 node 개수가 많은 상황에선 눈에 띄는 성능 차이를 보여줄 수 있습니다.

**단점**: html의 구조와 js의 로직이 분리되어 있기 때문에 js 파일 상으론 어떤 node를 생성하는 지 알 수 없습니다.

<br/><br/><br/>

**[포스팅 레퍼런스]**  
[DocumentFragment노드](https://programmer-seva.tistory.com/60)  
[XSS](https://ko.wikipedia.org/wiki/%EC%82%AC%EC%9D%B4%ED%8A%B8_%EA%B0%84_%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8C%85)  
[DocumentFragment를 이용한 JavaScript 성능 최적화](https://untitledtblog.tistory.com/44)  
[DOM이란 무엇인가?](https://velog.io/@surim014/DOM%EC%9D%B4%EB%9E%80-%EB%AC%B4%EC%97%87%EC%9D%B8%EA%B0%80)  
[Document Fragments and why you should use them](https://dev.to/ibn_abubakre/document-fragments-and-why-you-should-use-them-14fk)
