---
layout: post
title: '3. HTML과 CSS 그냥 코딩하면 되나요?'
subtitle: ' 최적화는 HTML과 CSS에서부터 '
date: 2020-08-15 20:21:00
author: 'Dev X'
header-img: 'img/post-bg/post-bg-CSS3_and_HTML5.png'
catalog: true
tags:
    - HTML
    - CSS
    - 반응형 디자인
---

![얼굴이 뭔가 이상한 피카츄](https://i.ibb.co/x3LZ8Q2/image.jpg)

_<center>HTML과 CSS도 계획없이 짜면 노답이다</center>_
<br/><br/><br/>

### 1\. PC, 모바일 웹과 반응형 웹

![네이버 PC웹과 모바일웹의 메인화면](https://i.ibb.co/LhDHGLp/image.png)

주소 앞에 .m 또는. mobile가 붙어있는 사이트들이 있습니다. 예를 들면 네이버는 스마트 폰에서 [naver.com](http://naver.com)에 접속을 해도 [m.naver.com](http://m.naver.com)이라는 주소로 자동 연결이 됩니다.

즉, naver에서는 접속하는 기기의 정보에 맞춰 가장 최적화된 버전의 사이트로 연결을 해주고 있습니다. **PC웹**과 **모바일 웹** 두 가지 사이트가 존재하고 있는 것이죠. 이런 사이트 분리 방식은 pc웹을 수정하지 않고 기기 호환이 가능하다는 점이 장점이지만, 반대로 지원 기기에 따라 사이트가 여러개 제작되어야 한다는 것이 단점이기도 합니다.
<br/><br/><br/><br/>

![Git의 반응형 웹 디자인 스크린샷](https://i.ibb.co/D8T4hk8/1.png)

그럼 Git은 어떨까요? [https://github.com/codestates](https://github.com/codestates)에 접속해 브라우저의 가로 너비를 줄여보시면 사이트 주소가 변경되지 않았는데도 레이아웃이 화면에 맞춰 변경되는 것을 볼 수 있습니다. 이런 기기 대응 방식을 **반응형 웹**이라고 합니다. 화면의 사이즈가 변경됨에 따라 레이아웃도 변경하는 방법입니다. 반응형 웹의 장점은 사이트를 여러개 제작하지 않고도 기기 호환이 가능하다는 점입니다. 하지만 물론 단점도 있죠. 반응형 웹은 하나의 HTML 페이지를 기준으로 CSS를 변경하는 것이기 때문에 PC와 모바일에 각각 다른 디자인을 지원하는 것이 힘듭니다.

**PC, 모바일 웹**과 **반응형 웹** 중 무엇이 더 좋다고 말하기는 힘듭니다. 네이버의 경우, 사이트를 개발할 인력이 충분했기 때문에 기기에 최적화된 디자인을 제공하기 위해 PC, 모바일 웹 방식을 택한 것으로 보입니다. 반대로 Git의 경우 기기에 따라 다른 정보를 제공할 필요성이 없으므로 반응형 웹 방식을 택한 것이죠.

PC, 모바일 웹을 제작하는 것은 어렵지 않습니다. 두 가지 디자인에 맞춰 사이트를 개발한 후 사용자 기기 정보에 맞춰 제공하기만 하면 되니까요. 이 글의 취지는 '개발자들은 왜 HTML과 CSS를 싫어하는가?'이기 때문에 반응형 웹에 대해서만 다루겠습니다.
<br/><br/><br/><br/><br/><br/>

---

### 2\. 반응형 웹의 제작 기술

실전이 가장 빠른 공부법이죠! 간단한 헤더와 탑 네비게이션을 예제로 천천히 살펴봅시다.
<br/><br/>

**디자인**
![목표 디자인의 PC뷰 스크린샷](https://i.ibb.co/vJnBRmF/2.png)

**HTML**

```html
<header>
    <h1><img src="./img/logo.png" alt="회사 로고" /></h1>
    <p>
        <a href="#">장바구니</a><a href="#">내 정보</a><a href="#">로그아웃</a>
    </p>
</header>
<nav>
    <ul>
        <li><a href="#">서비스 </a></li>
        <li><a href="#">투자정보 </a></li>
        <li><a href="#">고객지원 </a></li>
        <li><a href="#">채용 </a></li>
    </ul>
</nav>
```

**CSS**

```css
header {
    max-width: 1080px;
    margin: 0 auto;
    display: flex;
    justify-content: space-between;
    align-items: center;
}

h1 > img {
    width: 150px;
}

header a {
    font-size: 15px;
    margin-left: 12px;
}

nav {
    border-bottom: 1px solid rgba(0, 0, 0, 0.1);
}

nav ul {
    width: 1080px;
    margin: 0 auto;
    padding: 15px 0;
    display: flex;
    justify-content: space-around;
}
```

**max-width**로 가로 너비를 맞추고, **flex**로 li들을 배치했기 때문에 현재도 가로 400px까지 화면을 줄였을 때 글씨가 겹치거나 화면 밖으로 튀어나가진 않습니다.
<br/><br/>

![현재 모바일 뷰 스크린샷](https://i.ibb.co/y8bbt3M/3.png)

<center>현재의 모바일 기기 접속 화면입니다.</center>
<br/><br/>

![최종 모바일 뷰 스크린샷](https://i.ibb.co/TYwq3qm/6.png)

<center>우리가 목표로 하는 디자인입니다.</center>
<br/><br/><br/><br/>

#### 1) px과 rem

CSS의 단위에는 px, rem, em이 있습니다. px은 익숙해도 rem과 em이라는 단위는 낯선분들이 많으실 겁니다.

**px**: 고정 길이 단위. 다른 element의 영향을 받지 않고 항상 같은 길이를 유지합니다.  
**em**: 가변 길이 단위. 부모 element의 font-size를 기준로 백분율을 지정합니다. (1.2em = 부모의 120%) em은 부모에 따른 상대값이기 때문에 계산이 어려워 rem의 사용 빈도가 더 높습니다.  
**rem**: 가변 길이 단위. root em의 약자로 html의 font-size를 기준으로 백분율을 지정합니다. (1.2rem = html의 120%)

그렇다면 왜 rem을 반응형 디자인에 사용하는 걸까요?

px을 기준으로 CSS를 작성했을 때의 문제점을 생각해봅시다. 브라우저에는 글꼴 크기를 변경할 수 있는 설정이 있습니다. 시력이 좋지 않은 사람들을 위해 브라우저의 기본 글꼴 크기를 조절하는 배려이죠. 하지만 px으로 제작된 사이트는 글꼴 크기의 변경이 있을 시, 자식 element가 부모 element의 크기보다 커질 수 있어 의도한 디자인과 다른 화면이 제공될 수 있습니다. 이런 사태를 방지하기 위해 기본 글꼴 사이즈를 강제하는 사이트도 있지만 반응형 웹은 가능한 유연한 디자인으로 높은 접근성을 제공하는 것이 목적입니다. rem을 사용하는 이유가 하나 더 있지만 잠시 후에 알아보겠습니다.

**CSS**

```css
html {
    font-size: 62.5%; /* 1rem=10px */
}

body {
    font-size: 1.6rem; /* 16px */
}

header {
    max-width: 108rem;
    margin: 0 auto;
    display: flex;
    justify-content: space-between;
    align-items: center;
}

h1 > img {
    width: 15rem;
}

header a {
    font-size: 1.3rem;
    margin-left: 1.2rem;
}

nav {
    border-bottom: 1px solid rgba(0, 0, 0, 0.1);
}

nav ul {
    max-width: 108rem;
    margin: 0 auto;
    padding: 1.5rem 0;
    display: flex;
    justify-content: space-around;
}
```

브라우저의 기본 폰트 사이즈는 16px입니다. 즉, 기본적인 1rem은 16px의 값을 가집니다. 16px을 백분율로 매번 상대 값을 계산하는 것은 복잡하기 때문에 편의를 위해 **html**의 폰트 사이즈를 16px의 **62.5%**, **10px**로 변경했습니다. 그리고 다시 body에 **1.6rem**을 폰트 사이즈로 설정하면 화면상 변화는 없겠지만 10px을 1 rem이라는 단위로 사용할 수 있습니다.

그리고 거의 모든 px단위를 rem단위로 쉽게 변경했습니다. 예외로 border에는 rem단위를 주지 않았습니다. 1px은 아주 작은 단위이기도 하고 1px보다 더 작은 크기는 눈으로 확인하기 어렵기 때문에 디자인상의 이유로 px을 사용해야 할 때도 있습니다. 이처럼 가변 단위와 고정 단위를 사용해야 할 때를 적절히 선택해야 합니다.

또 다른 가변 단위에는 vh, vw도 있습니다.  
**vh**: viewport height의 약자로 브라우저 화면의 세로 값을 100으로 사용하는 가변 단위입니다.  
**vw**: viewport width의 약자로 브라우저 화면의 가로 값을 100으로 사용하는 가변 단위입니다.
<br/><br/>

#### 2) 미디어 쿼리

미디어 쿼리는 기기의 정보에 따라 다른 CSS를 제공해야할때 사용하는 구문입니다.

**CSS**

```css
@media media-type and (media-feature-rule) {
    /* CSS rules go here */
}
```

**media-type:** 제공할 media의 타입을 지정합니다. 타입은 all(모든 디바이스), print(출력), screen(전자기기), speech(음성)등이 있지만 반응형 디자인에 필요한 것은 **screen** 속성입니다.  
**media-feature-rule:** 미디어 쿼리를 제공할 규칙을 정합니다. width, max-width, min-width, orientation(가로 or 세로 모드)등의 속성이 있습니다.

**CSS**

```css
@media screen and (max-width: 400px) {
    header a {
        display: none;
    }

    nav ul {
        height: 0;
        display: none;
        visibility: none;
    }
}
```

우리가 제작 중인 모바일 디자인은 nav를 숨기고 햄버거 메뉴를 지원하기 때문에 위와 같은 미디어 쿼리를 CSS 파일 가장 끝에 추가했습니다. **max-width**는 기기의 **너비가 400px 이하 일때**를 의미합니다. 즉, 가로너비 400px 이하의 기기에서 사이트를 접속했을 때 header의 링크들과 nav의 메뉴들을 숨기는 코드입니다.

그리고 반응형 디자인에서 rem을 사용하는 이유가 하나 더 있다고 말했었습니다. 그 이유는 rem과 미디어 쿼리를 함께 사용하면 더 유연한 디자인이 제공 가능하기 때문입니다. rem은 기기에 따라 상대적인 길이 값을 제공하지만 폰트 사이즈의 변화와 우리가 원하는 디자인의 크기가 rem만으로 충족되지 않을 때도 있습니다. 웹에선 적당했던 사진 크기가 모바일에선 너무 크거나 하는 경우 말이죠. 그럴땐 rem이 html의 폰트 사이즈를 기반으로 한다는 사실을 이용하면 편리하게 반응형 웹을 개발할 수 있습니다.

**CSS**

```css
@media screen and (max-width: 400px) {
    html {
        font-size: 50%;
    }

    header a {
        display: none;
    }

    nav ul {
        height: 0;
        display: none;
        visibility: none;
    }
}
```

html의 폰트 사이즈를 62.5%에서 50%로 줄이는 것 만으로 rem을 사용한 다른 element의 크기도 12.5%만큼 감소시킬 수 있습니다. HTMl코드의 수많은 element들을 일일이 수정하지 않고도 크기를 수정하는 방법은 반응형 웹 개발에 들어가는 수고를 크게 덜어줄 것입니다. 이것이 가변 element엔 rem을 반드시 사용해야 하는 이유이겠죠?

![작업중인 헤더의 스크린샷](https://i.ibb.co/3y9YJSc/5.png)
_<center>현재 header의 모습입니다.</center>_

<br/><br/><br/><br/>

#### 3) 반응형을 염두한 HTML 구조의 중요성

혹시 코드를 따라쓰고 계셨다면 슬슬 이상함을 느끼셨을 것입니다. 우리가 목표로 하는 디자인과 거의 비슷해졌지만 메뉴 버튼이 보이질 않습니다. 모바일 웹 디자인을 염두에 두지 않고 HTML 작성을 했을 시 종종 일어나는 실수입니다. 반응형 웹의 단점 중 하나가 HTML 코드 하나로 여러가지 CSS를 제공해야하기 때문에 디자인의 큰 변경이 불가능하다는 것이었습니다. 마찬가지로 모바일 웹에만 드러나는 HTML element는 PC 뷰에서도 존재해야 합니다. 변경될 디자인을 염두해 HTML을 작성했었다면 menu 버튼을 빠트리는 일이 없었겠죠? 다시 HTML과 CSS를 수정합시다.

**HTML**

```html
<header>
    <h1><img src="./img/logo.png" alt="회사 로고" /></h1>
    <div>
        <a href="#">장바구니</a><a href="#">내 정보</a><a href="#">로그아웃</a>
        <button>
            <img src="./img/button.png" alt="햄버거버튼" class="menu_btn" />
        </button>
    </div>
</header>
```

링크들을 div로 묶고 끝에 button을 추가 했습니다.

**CSS**

```css
/* 미디어쿼리 상위에 추가 */
header button img {
    width: 3rem;
    display: none;
    margin-right: 1rem;
}

/* 미디어쿼리 수정 */
@media screen and (max-width: 400px) {
    ... header button img {
        display: block;
    }
}
```

모바일 뷰가 아닌 상태에선 button이 보이지 않도록 미디어 쿼리를 작성했습니다.

![헤더 완성 스크린샷](https://i.ibb.co/TYwq3qm/6.png)
_<center>완성된 헤더</center>_

이렇게 메뉴 버튼이 생겼습니다. 버튼을 눌렀을 때 숨겨진 메뉴가 드러나는 기능을 추가할 수 있겠지만 반응형 웹을 제작할 때 사용하는 주요 기술들에 대해서는 설명을 마쳤기에 생략하겠습니다.
<br/><br/><br/><br/>

#### 4) 반응형 이미지

그 외에도 반응형 웹을 제작할 때는 이미지 크기에 대한 고려도 해야합니다. 요즘엔 모바일로도 빠른 속도의 무선 인터넷을 사용 가능하지만, 그렇다고 접속할 때마다 많은 데이터를 사용해야하는 사이트는 상대적으로 속도가 느리기도 하고, 통신 요금이 적은 사용자들이 좋아하지 않겠죠?

반응형 이미지를 지원하기 위해선 우선 여러 크기의 이미지 파일이 필요합니다. 이미지 파일을 다운 받았을때 파일 명 끝에 small, large등의 단어가 붙은 것을 본 적 있으실 겁니다. 이런 접미사는 파일의 크기와 해상도의 크기 차이를 의미합니다. 각 해상도마다 지원하고자 하는 기기의 대상이 정해져 있겠죠? 사용법은 미디어 쿼리와 유사합니다.

```html
<img srcset="URL 이미지_너비w" size="이미지_표시_크기" />
```

```html
<header>
    <h1>
        <img srcset="./img/logo-s.png 60w, ./img/logo-m.png 70w,
        ./img/logo-l.png 80w, size = "(max-width: 100px) 10vw" alt="회사 로고">
    </h1>
    <div>
        <a href="#">장바구니</a><a href="#">내 정보</a><a href="#">로그아웃</a>
        <button>
            <img src="./img/button.png" alt="햄버거버튼" class="menu_btn" />
        </button>
    </div>
</header>
```

반응형 이미지에 대한 더 자세한 이야기는 아래에서 확인하실 수 있습니다.

[반응형 이미지 MDN](https://developer.mozilla.org/ko/docs/Learn/HTML/Multimedia_and_embedding/Responsive_images)  
[HTML 반응형 이미지 문법 가이드](https://webactually.com/2020/07/html-%EB%B0%98%EC%9D%91%ED%98%95-%EC%9D%B4%EB%AF%B8%EC%A7%80-%EB%AC%B8%EB%B2%95-%EA%B0%80%EC%9D%B4%EB%93%9C/)

#### 마치며

요즘엔 거의 모든 사이트가 반응형 웹을 제공하기 때문에, 반응형 웹을 개발 할 수 없다면 HTML과 CSS를 잘한다고 말하기 어렵습니다. 특히 3번의 반응형 style을 염두해둔 HTML 구조는 한 번 실수를 하면 HTML 구성과 CSS를 두루 수정하다못해 코드가 꼬여 처음부터 다시 짜는게 빠른 상황이 벌어지기도 합니다. HTML과 CSS도 코딩에 들어가기 전에 디자인에 맞춘 설계를 해야 실수를 하는 일이 줄겠죠? 벌써 머리가 아프지만 시리즈는 마지막 4편 SEO로 이어집니다.
<br/><br/>

**[HTML과 CSS 그냥 코딩하면 되나요? 시리즈]**  
[1. HTML과 CSS가 쉽지만 어려운 이유](https://dkje.github.io/2020/08/12/IsHTMLandCSSReallyEasy1/)  
[2. 웹표준과 웹 접근성](https://dkje.github.io/2020/08/12/IsHTMLandCSSReallyEasy2/)  
[3. 반응형 웹](https://dkje.github.io/2020/08/15/IsHTMLandCSSReallyEasy3/)
<br/><br/><br/><br/>

<center>
<span style="color:grey">
html과 css을 옛날에 배운 사람이<br/>
legacy 지식을 업데이트 할 목적으로 정리한 글입니다.<br/>  
잘못된 부분이 있다면 편히 지적해주세요!
</span></center>

<br/><br/>
