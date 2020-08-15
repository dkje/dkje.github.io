---
layout: post
title: '2. 웹표준과 웹접근성'
subtitle: ' 그때부터였을까.. HTML CSS가 까칠해지기 시작한게... '
date: 2020-08-11 23:40:00
author: 'Dev X'
header-img: 'img/post-bg/post-bg-CSS3_and_HTML5.png'
catalog: true
tags:
    - HTML
    - CSS
    - 웹표준
    - 반응형 디자인
    - SEO
---

![나는 HTML과 CSS 마스터야! 최고의 개발자이지!](https://i.ibb.co/0cN0Lm0/1.jpg)
_<center>왜 그렇게 쳐다보세요..?</center>_
<br/><br/>

## HTML과 CSS 코딩할 때 고려해볼 세 가지

### 웹 표준과 웹 접근성

웹 접근성과, 웹 표준 개념이 한국에 들어온 지 꽤 많은 시간이 지났습니다. 권장 지침이기 때문에 대수롭지 않게 생각할 수 있지만 공공 또는 일부 민간 사이트에선 웹 접근성을 보장할 것이 [의무화](http://www.websoul.co.kr/accessibility/laws.asp)되어있습니다. 나는 웹 접근성 의무가 없는데 굳이 지켜야 하나..? 싶을 수도 있지만 **웹 표준은** **반응형 디자인**과**SEO**로 연결되어있습니다. 웹 표준을 잘 따르면구 버전의 브라우저와 다양한 기기에서 사이트를 접속하는 것에 문제가 없고, 정확한 문서 정보를 제공하기 때문에 검색 효율성을 높일 수도 있습니다. 1타 3 피라는 점에서 HTML, CSS 코딩을 할 때 웹 표준을 지키지 않을 이유가 없겠죠?

그렇다면 웹 표준은 어떻게 지켜야 할까요?
<br/><br/><br/>

#### 1) 적절한 HTML 태그의 사용

![평범한 index.html 페이지](https://i.ibb.co/M6QxL6h/image.png)

콘솔 창에 error 표시 하나 없는 깨끗한 웹 페이지입니다.
과연 이 사이트의 index.html 파일도 깨끗할까요?

```html
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8" />
        <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    </head>
    <title>Site Title</title>

    <body>
        <div id="header">
            <img src="" />
            <h1>HTML CSS 너무 어렵다</h1>
        </div>
        <form>
            <h1>로그인</h1>
            <p>이름</p>
            <input type="text" />
            <p>비밀번호</p>
            <input type="password" />
        </form>
        <input type="submit" value="제출" id="btn" />
    </body>

    <link rel="stylesheet" href="css/styles.css" />
</html>
```

사실 제가 엉망진창으로 짜 본 html 파일이었습니다. 보기엔 아무 에러 표시도 없고 style 또한 잘 적용되었기 때문에 아무 문제 없는 페이지라고 오해할 수 있습니다.

위에서부터 다시 코드를 읽어봅시다.

-   1\. 문서의 메타 데이터에 해당하는 title이 head의 태그 밖에 벗어나 있습니다.
-   2\. div 요소가 대문자로 선언되었습니다.
-   3\. 또 div에 id로 header를 명시하기보단 header 요소를 사용할 수 있겠습니다.
-   4\. img 요소에 이미지가 로딩되지 않았을 시 이미지를 설명할 alt 속성이 지정되지 않았습니다.
-   5\. 한 파일에 사이트의 제목을 뜻하는 h1이 두 번 선언되었습니다.
-   6\. form을 전송할 주소 값인 action이 선언되지 않았습니다.
-   7\. input의 용도를 설명하는 label대신 p가 사용되고 있습니다.
-   8\. form의 제출 용도인 submit버튼이 form의 바깥에 위치해있습니다.

```html
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8" />
        <meta name="viewport" content="width=device-width, initial-scale=1.0" />
        <link rel="stylesheet" href="css/styles.css" />
        <title>Site Title</title>
    </head>

    <body>
        <header>
            <img src="./img/img.png" alt="로고 이미지" />
            <h1>HTML CSS 너무 어렵다</h1>
        </header>
        <form action="..." method="get">
            <h2>로그인</h2>
            <label for="name">이름</label>
            <input type="text" name="name" />
            <label for="password">비밀번호</label>
            <input type="password" name="password" />
            <input type="submit" value="제출" id="btn" />
        </form>
    </body>
</html>
```

웹 표준에 맞춰 다시 작성한 코드입니다. 이렇게 HTML 코드를 수정해도 사이트를 새로고침 했을 때 큰 변화는 없을 것입니다. 하지만 사이트를 이용할 누군가에겐 큰 차이가 있을 수도 있습니다. 웹사이트 이용자 중엔 마우스나 모니터를 사용할 수 없는 사람도 있습니다. 모두에게 동일한 정보를 전달해야 한다는 웹 접근성의 취지를 이해하신다면 웹 접근성이 갖고 있는 강제성이 납득되실 겁니다.

웹표준에 대한 더 자세한 정보는 **[웹접근성과 웹표준](https://seulbinim.github.io/WSA/accessibility.html)**에서 참고하실 수 있습니다.  
내가 웹 표준을 잘 지켰는지 확인하고 싶다면 **[W3C 통합 검사기](https://validator.w3.org/unicorn/?ucn_lang=ko)**에서 검사가 가능합니다.
<br/><br/><br/>

#### 2) 브라우저와 기기 호환성

CSS는 꾸준히 발전해왔지만 오래된 브라우저는 새로운 HTML과 CSS 기술을 이해하지 못합니다. 크롬과 파이어폭스, 에지 등의 최신 브라우저는 grid와 flex를 지원하지만 구버전 IE에서 grid를 사용한 사이트에 접속한다면 모든 레이아웃이 일그러진 것을 볼 수 있을 것입니다. 내가 지원해야 할 브라우저에서 이 속성을 사용해야 될지 고민이 된다면 **Can I use**에서 검색해 보시는 것을 추천드립니다.

##### [Can I Use](https://caniuse.com/)

![Can I use의 접속 화면](https://i.ibb.co/cc3ft9z/2020-08-16-034918.png)
![Can I use의 flex 검색 결과](https://i.ibb.co/T4SF8gW/1.png)
flex를 검색해보았습니다. 사용자 퍼센티지를 보니 97.84%의 인터넷 이용자가 flex가 지원되는 브라우저를 사용하고 있는 것을 확인할 수 있습니다. IE의 11, 10 버전에서도 부분적 지원을 하고 있는 것 같습니다만 커서를 올려 자세한 내용을 확인해보니 '일부 지원을 하지만 많은 버그가 발생하는 상황'이라고 합니다. IE를 지원해야 하는 프로젝트에선 다른 방법으로 레이아웃을 구현해야만 합니다.

다양한 브라우저를 지원하기 위해선 css 속성 앞에 '**벤더 프리픽스**'라는 마크업을 추가하기도 합니다. css를 보다 보면 -moz-나 -webkit- 또는 -ms-라는 접두어를 붙여 같은 속성을 여러 번 선언하는 것을 본 적 있으실 겁니다.

```css
background: -webkit-gradient(top, white, black); <!--크롬과 사파리-->
background: -moz-linear-gradient(top, white, black); <!--파이어폭스-->
background: -ms-linear-gradient(top, white, black); <!--익스플로러-->
background: -o-linear-gradient(top, white, black); <!--오페라-->
background: linear-gradient(top, white, black);
```

아직 브라우저에서 완벽하게 지원되지 않는 새로운 기능을 실험 삼아 사용해보고 싶을 때, 브라우저별 접두어를 속성 앞에 붙여주면 해당 기능을 사용할 수 있습니다. Can I use에서 검색했을 때 벤더 프리픽스를 사용 시 지원 가능이라는 조건이 붙어있다면, 프리픽스를 붙여 그 기능을 사용하시면 됩니다!

하지만 일일이 프리픽스를 적는 것은 단순 노동에 정말 귀찮은 일이 아닐 수 없습니다.. 모두가 같은 마음이기에 css를 입력하면 알아서 벤더 프리픽스를 추가해주는 툴이 있습니다.

---

##### [Autoprefixer](https://autoprefixer.github.io/)

![Autoprefixer의 접속화면](https://i.ibb.co/jwXhPc3/Screenshot-2020-08-16-at-03-51-01.jpg)
**Autoprefixer**는 웹사이트에서 코드 변환이 가능하고, webpack의 플러그인으로도 존재합니다. 프로젝트의 환경에 맞춰 사용하면 작업 시간을 단축시켜 줄 것입니다. 그리고 더한 흑마법도 존재합니다..

---

##### [PostCSS](https://postcss.org/)

![POSTCSS의 로고](https://i.ibb.co/nk6Tymt/Screenshot-2020-08-16-at-03-51-22.jpg)
**PostCSS**는 모던 CSS를 사용할 때 필수적인 프레임워크입니다. POST CSS은 CSS 작업 환경을 향상할 다양한 플러그인을 지원합니다. autoprefixer 또한 POSTCSS의 플러그인 중 하나이고, PostCSS Preset Env은 작성한 css 코드가 지원하고 싶은 버전의 브라우저에서 사용이 가능하도록 컴파일해주는 플러그인입니다. 또 CSS Modules를 사용하면 css에서 class를 중복 정의해 벌어질 수 있는 문제를 각 class가 고유 값을 가지도록 변환해 방지할 수 있습니다. **PostCSS**는 그 외에도 많은 플러그인을 지원하니 공식 홈페이지를 방문해 보시는 것을 추천드립니다.
<br/><br/><br/>

**\[Posting Reference\]**  
[웹 접근성과 웹 표준](https://seulbinim.github.io/WSA/standards.html#%EC%9B%B9%ED%91%9C%EC%A4%80-%EC%9D%B4%EB%9E%80)
<br/>

**[HTML과 CSS 그냥 코딩하면 되나요? 시리즈]**  
[1. HTML과 CSS가 쉽지만 어려운 이유](https://dkje.github.io/2020/08/12/IsHTMLandCSSReallyEasy1/)  
[2. 웹표준과 웹 접근성](https://dkje.github.io/2020/08/12/IsHTMLandCSSReallyEasy2/)
<br/><br/><br/>

<center>
<span style="color:grey">
html과 css을 옛날에 배운 사람이<br/>
legacy 지식을 업데이트 할 목적으로 정리한 글입니다.<br/>  
잘못된 부분이 있다면 편히 지적해주세요!
</span></center>

<br/><br/>
