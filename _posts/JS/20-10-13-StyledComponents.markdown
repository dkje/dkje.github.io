---
layout: post
title: "[React] Styled Components 사용기"
subtitle: " "
date: 2020-10-13 08:56:00
author: "Dev X"
header-img: "img/post-bg/post-bg-styled-components.png"
published: true
catalog: true
tags:
  - JS
  - React
  - styled-components
---

이 글은 styled components에 대해 공부하며 정리하는 목적으로 작성 되었습니다. 잘못된 정보는 지적해주시면 바로 정정하겠습니다!

## 🎀 styled-components

![styled-components logo](https://i.ibb.co/ydkG6cv/img.png)

**styled components**는 javascript에서 css를 사용 할 수 있도록 도와주는 **스타일링 프레임워크**입니다. React Component에 특정 스타일링을 할 수 있기 때문에 재사용성을 더 높일 수 있고, javascript에 영향을 받는 스타일링을 간편하게 구현 할 수 있습니다. 


### 사용 예시

**기존 CSS 스타일링 방식**
```js
  const divStyle = {
    backgroundColor: "black",
    width: "100px",
    height: "100px",
  };

  return <div style={divStyle}></div>
```

**styled-components를 사용한 방식**
```js
  const StyledDiv = styled.div`
    background-color: black;
    width: 100px;
    height: 100px;
  `;

  return <StyledDiv></StyledDiv>
```
기존의 React Component 스타일링은 **style 속성**에 객체를 전달하거나 **className**을 설정하고 별도의 css 파일을 import하는 방식으로 사용해왔습니다.

**styled-components**는 style이 적용된 Component를 직접 생성하기 때문에, 스타일링을 위한 코드 사용량이 줄어드는 효과가 있습니다. 또 key value의 형태가 아닌 css의 문법을 그대로 사용하기 때문에 기존 css의 사용법보다 **가독성**도 높습니다.

### 장점

#### 1. component 단위 스타일링
```js
<div class="sc-bdnylx iaxDju"></div>
```
```css
.iaxDju {
    background-color: black;
    width: 100px;
    height: 100px;
    display: none;
}
```
styled-components로 생성된 Components들을 빌드하면 <u>임의의 클래스 명에 스타일이 적용</u>되어 있는 것을 확인 할 수 있습니다. styled-components는 **중복되지 않는 특정 class명**을 생성해 스타일을 적용하기 때문에, className이 중복되거나 selector의 우선 적용 순위 때문에 css 스타일링이 <u>혼선을 일으키는 사고를 방지</u> 할 수 있습니다.

#### 2. 조건부 스타일링
```js
  const StyledDiv = styled.div`
    background-color: black;
    width: 100px;
    height: 100px;
    ${({ login }) => {
      return login ? `display: none` : null;
    }}
  `;

  return <StyledDiv login={true}></StyledDiv>;
```
styled-components는 Component의 **props**를 전달받아 사용하는 것이 가능합니다. 템플릿 리터럴 내에서 javascript를 사용하는 것과 같은 형식이며, 내부에서 선언된 함수는 props를 파라미터로 실행됩니다.


#### 3. 확장 스타일링

```js
const Container = styled.div`
    max-width: 600px;
    width: 100%;
    height: 100px;
  `;

  const BlackContainer = styled(Container)`
    background-color: black;
  `;

  const RedContainer = styled(Container)`
    background-color: red;
  `;

  return (
    <>
      <BlackContainer />
      <RedContainer />
    </>
  );
```

styled-components는 새로운 Component를 선언하는 것 뿐만 아니라, **기존의 Component에 스타일을 추가**하는 것도 가능합니다. 확장 스타일링을 사용하면 <u>중복된 코드 양을 줄이고, 분산된 스타일을 일관적으로 관리</u> 할 수 있어 유지보수 비용을 줄일 수 있습니다.

```js
const MyLink = styled(Link)`
    color: black;
    text-decoration: none;
  `;

  return (
    <Router>
      <MyLink to="/main"> 커스텀 링크 </MyLink>
    </Router>
  );
```

기존 Component에 스타일을 추가 할 수 있는 기능 덕분에, **서드 파티 Component**와도 호환이 가능합니다. 예를 들면 자주 사용하는 React-router-dom의 Link Component의 경우에도 위와 같은 방법으로 스타일을 적용해 사용 할 수 있습니다.


#### 4. 중첩 스코프

```js
const StyledDiv = styled.div`
    background-color: black;
    width: 100px;
    height: 100px;
    p {
      color: white;
    }
  `;

  return (
    <>
      <StyledDiv>
        <p>Title</p>
      </StyledDiv>
      <p>content</p>
    </>
  );
```
SASS의 **중첩 스코프 규칙**을 사용할 수 있습니다. 덕분에 내부의 모든 component를 styled-components로 생성하지 않아도, **하위 컴포넌트에게만 적용하고 싶은 스타일**을 스코프 형태로 구현 할 수 있습니다.
(주의: 모든 SASS 문법이 사용 가능하진 않습니다.)


## 🔧 프로젝트 세팅하기

![프로젝트 완성 이미지](https://i.ibb.co/Vph0yHL/b.gif)

<center>styled-components를 사용해 가장 기초적인 스타일링 세팅을 진행하겠습니다.</center> 
<center>두 가지 테마 관리와 반응형 디자인 설정도 함께 합니다.</center>
<center> [Project Repository](https://github.com/dkje/basic-styled-components) </center>

### 1. Install
```js
// CRA로 프로젝트 생성
$ create-react-app styled-components-project

// styled-components 설치하기
$ npm install --save styled-components
// yarn을 사용한다면
$ yarn add styled-components
```

CRA로 앱을 생성한 후 styled-components를 설치합니다.

```js
import styled from "styled-components";
...
const CustomDiv = styled.div`
  ...
`;
```
앞으로 스타일링된 Component를 생성 할 때는 **styled**라는 이름으로 styled-components 모듈을 import해, 위와 같은 문법으로 정의합니다.


### 2. theme

스타일링을 시작하기 앞서, 자주 사용하게 될 **색상 코드, 사이즈, 폰트, 미디어 쿼리** 등의 정보를 변수로 생성해 사용하면 일관적인 스타일 관리가 가능합니다. styled-component에선 전반적으로 프로젝트 전체에서 사용할 목적의 변수 모음 객체를 **theme**이라고 부릅니다.

<span style="color:grey">
SASS 사용 경험이 있으시다면, variable을 별도의 파일로 분리해 main 파일에서 관리하던 것과 유사한 목적으로 사용되는 객체입니다!
</span>

**/src/styles/theme.js**
```js
const margins = {
  sm: ".5rem",
  base: "1rem",
  lg: "2rem",
  xl: "3rem",
};

const paddings = {
  sm: ".5rem",
  base: "1rem",
  lg: "2rem",
  xl: "3rem",
};

const fonts = {
  family: {
    base: `'Noto Sans KR', sans-serif`,
    title: `'Merriweather', serif`,
  },
  size: {
    sm: "1.4rem",
    base: "1.6rem",
    lg: "2rem",
    xl: "2.5rem",
    title: "6rem",
  },
  weight: {
    light: 100,
    normal: 400,
    bold: 700,
  },
};

const colors = {
  red: "#ff4d4d",
  yellow: "#ffff4d",
  blue: "#0099ff",
};

const size = {
  mobile: "425px",
  tablet: "768px",
  desktop: "1440px",
};

// 미디어 쿼리의 중복 코드를 줄이기위해 정의된 변수입니다
const device = {
  mobile: `@media only screen and (max-width: ${size.mobile})`,
  tablet: `@media only screen and (max-width: ${size.tablet})`,
  desktopL: `@media only screen and (max-width: ${size.desktop})`,
};

//테마에 따라 다른 값을 갖는 색상 값입니다 
const lightThemeColors = {
  ...colors,
  primary: "#333",
  secondary: "#fff",
  tertiary: "#808080",
};

const darkThemeColors = {
  ...colors,
  primary: "#fff",
  secondary: "#333",
  tertiary: "#d4d0c4",
};

// 테마와 관련없이 공통으로 사용되는 변수들입니다
const defalutTheme = {
  margins,
  paddings,
  fonts,
  device,
};

// 각 테마는 공통 변수와 함께, 각기 다른 색상 값들을 갖습니다.
export const darkTheme = {
  ...defalutTheme,
  colors: darkThemeColors,
};

export const lightTheme = {
  ...defalutTheme,
  colors: lightThemeColors,
};
```


**/src/components/App.js**
```js
import React, { useState } from "react";
import { ThemeProvider } from "styled-components";
import { darkTheme, lightTheme } from "../styles/Theme";
import Header from "./Header";
import Container from "./Container";

const App = () => {
  const [theme, setTheme] = useState(lightTheme);
  const [currentThemeText, setCurrentThemeText] = useState("Light Theme");

  // 각 theme은 state로 관리되며 버튼 클릭 이벤트 시 변경됩니다.
  const switchTheme = () => {
    const nextTheme = theme === lightTheme ? darkTheme : lightTheme;
    setTheme(nextTheme);
    const nextThemeText = theme === lightTheme ? "Dark Theme" : "Light Theme";
    setCurrentThemeText(nextThemeText);
  };

  return (
    //ThemeProvider는 반드시 theme을 사용 할 하위 Component를 감싸고 있어야 합니다.
    <ThemeProvider theme={theme}>
      <Header switchTheme={switchTheme} />
      <Container currentThemeText={currentThemeText} />
    </ThemeProvider>
  );
};

export default App;

```

이렇게 생성한 **theme** 객체는 각 Component마다 import해 사용 할 수도 있지만, 더 간단하게 사용할 수 있는 방법이 있습니다! 바로 styled-components가 지원하는 **[ThemeProvider](https://styled-components.com/docs/tooling#themeprovider)**를 사용하는 것입니다.

- **ThemeProvider**: 반드시 theme 속성을 갖는 컴포넌트. theme 속성의 값으로 설정된 객체를 모든 하위 컴포넌트들의 props로 전달합니다.

이렇게 **ThemeProvider**를 사용하면 굳이 props나 useContext를 사용하지 않아도 <u>모든 컴포넌트에서 theme 객체에 간편하게 접근</u> 할 수 있습니다. 또 사용자 **theme 변경 이벤트**가 발생 했을시, <u>Provider의 theme 속성 값만 변경하는 것으로 간단하게 테마 변경을 구현</u> 할 수 있습니다.

### 3. global-style

#### reset, normalize, css-file
styled-componenst에서는 Component 단위가 아닌 <u>HTML Elements 단위로 적용되는 style</u>을 **global-style**에서 관리합니다. 예를 들면 **reset**, **normalize** 등의 기본적인 스타일이 이 경우에 포함됩니다.

```js
// reset과 normalize 설치
$ npm install --save styled-reset
$ npm install --save styled-normalize
// yarn을 사용한다면
$ yarn add styled-reset
$ yarn add styled-normalize

// 별도의 sass의 파일을 사용한다면 node-sass 설치
$ npm install --save node-sass
$ yarn add node-sass
```

- [styled-reset](https://www.npmjs.com/package/styled-reset): [Eric Meyer's Reset CSS](https://meyerweb.com/eric/tools/css/reset/)을 npm 모듈로 설치해 사용 할 수 있습니다.

- [styled-normalize](https://www.npmjs.com/package/styled-normalize): npm 모듈로 지원되는 normalize입니다.

스타일링에 기본적으로 세팅하는 reset은 npm 모듈로 간편하게 설치해 불러 올 수 있습니다. <u>반드시 위의 모듈을 사용할 필요는 없으며</u>, 개인적으로 설정해둔 css, scss 파일이 있다면 그 또한 import해 사용 할수 있습니다.

**/src/styles/customReset.scss**
```scss
html,
body {
  width: 100%;
  height: 100%;
}

html {
  font-size: 62.5%; //1rem = 10px;
}

body {
  font-size: 1.6rem;
}
```

#### createGlobalStyle

**/src/styles/GlobalStyle.js**
```js
import { createGlobalStyle, css } from "styled-components";
import reset from "styled-reset";
import customReset from "./customReset.scss";

const GlobalStyle = createGlobalStyle`
    ${reset};
    ${customReset};

    html {
      font-size: 62.5%; //1rem = 10px;
    }

    ${({ theme }) => {
      return css`
        body {
          font-family: ${theme.fonts.family.base};
          font-weight: ${theme.fonts.weight.normal};
          font-size: ${theme.fonts.size.base};
        }
      `;
    }}
`;

export default GlobalStyle;

```

styled-components가 지원하는 **[createGlobalStyle](https://styled-components.com/docs/api#createglobalstyle)**를 이용하면 페이지 전체에 적용될 스타일을 선언 할 수 있습니다. 템플릿 리터럴에 일반적인 css와 같은 문법으로 사용하면 되기 때문에 기존의 코드를 쉽게 재사용할 수 있습니다.

- **6,7번 줄**: css 파일이나 별도로 설치한 스타일 모듈도 import해 사용할 수 있습니다. 템플릿 리터럴 안에서 import된 모듈 변수명를 불러오면 됩니다.

- **9번 줄**: 그 외에 추가적으로 선언하고 싶은 스타일이 있다면 css 문법으로 편리하게 선언 가능합니다.

- **13번 줄**: props와 관련된 스타일은 함수로 선언합니다. 선언된 함수는 Component가 전달받은 <u>props들을 파라미터로 실행</u>됩니다. 위의 코드에선 props중 theme 속성을 구조 분해 할당시켜 사용했습니다. 
  
- **14번 줄**: return 값은 string으로 인식되기 때문에 반드시 css 리터럴 선언이 필요하진 않습니다. 하지만 css 리터럴 선언을 사용하면 formatter가 다시 스타일 코드를 인식하게 되어 가독성과 줄정렬에 도움이 됩니다.


**/src/components/App.js**
```js
...
return (
    <ThemeProvider theme={theme}>
      <GlobalStyle />
      <Header switchTheme={switchTheme} />
      <Container currentThemeText={currentThemeText} />
    </ThemeProvider>
  );
```

정의된 **GlobalStyle** 컴포넌트는 theme을 props으로 받을 수 있도록 **ThemeProvider의 하위 컴포넌트**로 배치합니다.

### 4. Styled-Component 정의

이제 설정된 global-style과 theme을 이용해 컴포넌트를 스타일링 해보겠습니다.

#### Header
**/src/styles/Header-style.js**
```js
import styled, { css } from "styled-components";

// theme을 변경하는 btn의 스타일링
const ThemeSwitchBtn = styled.button`
  ${({ theme }) => {
  //props로 전달받은 theme 속성을 사용한다.
  // App의 theme state가 변경되면 컴포넌트가 재 렌더링되며 다른 색상 값들을 갖게 된다.
    return css`
      background-color: ${theme.colors.primary};
      color: ${theme.colors.secondary};
      font-size: ${theme.fonts.size.base};
      border-radius: 2px;
    `;
  }}
`;

const CustomHeader = styled.div`
  ${({ theme }) => {
    return css`
      padding: 1rem;
      display: flex;
      justify-content: center;
      background-color: ${theme.colors.secondary};
    `;
  }}
`;

const styledComponents = { ThemeSwitchBtn, CustomHeader };

export default styledComponents;
```

styled-components 선언은 Component 함수 스코프 외부에 위치 해야하는 규칙을 갖고 있습니다. 함수 내에 선언한다고 오류가 발생하지는 않지만, 콘솔에 **Component와 styled-component를 분리**하길 권장하는 경고 메시지가 표시됩니다. 또 스타일 코드는 Component의 props와 state에 영향을 주지 않기 때문에 별도의 파일로 분리해 관리하겠습니다.


**/src/components/Header.js**
```js
import React from "react";
import styledComponents from "../styles/Header-style";
const { CustomHeader, ThemeSwitchBtn } = styledComponents;

const Header = ({ switchTheme }) => {
  return (
    <CustomHeader>
      <ThemeSwitchBtn
        onClick={() => {
          switchTheme();
        }}
      >
        Change Theme
      </ThemeSwitchBtn>
    </CustomHeader>
  );
};

export default Header;
```

스타일 코드를 별도로 분리한 덕에 코드 가독성이 좋아졌습니다. 

- **CustomHeader**: styled-components 선언에서 theme 속성을 이용했기 때문에 theme 변경 이벤트시 재 렌더링 되며 다른 스타일 값을 갖게 됩니다.
- **ThemeSwitchBtn**: 클릭시 switchTheme를 실행시켜 App의 theme state를 변경합니다. 마찬가지로 theme에 영향을 받는 스타일 속성을 갖고있어, theme 변경시 다른 스타일로 표시됩니다.

#### Container

**/src/styles/Container-style.js**
```js
import styled, { css } from "styled-components";

const CustomContainer = styled.div`
  ${({ theme }) => {
    const { colors, device, fonts, paddings } = theme;
    return css`
      width: 100%;
      height: 100%;
      background-color: ${colors.tertiary};
      ${device.tablet} {
        background-color: ${colors.red};
      }

      h1 {
        font-size: ${fonts.size.xl};
        padding: ${paddings.xl};
        text-align: center;
      }
    `;
  }}
`;

const styledComponents = { CustomContainer };

export default styledComponents;
```
- **10번 줄**: theme에 선언했던 미디어 쿼리를 사용해 코드를 축약시켰습니다. 브라우저의 너비가 tablet에 속할 경우 배경 색이 변경됩니다.
- **14번 줄**: styled-components의 중첩 선언입니다. CustomContainer 하위에 위치하는 h1 요소만 이 스타일 코드에 영향을 받습니다.
 

**/src/components/Container.js**
```js
import React from "react";
import styledComponents from "../styles/Container-style";

const { CustomContainer } = styledComponents;

const Container = ({ currentThemeText }) => {
  return (
    <CustomContainer>
      <h1>{currentThemeText}</h1>
    </CustomContainer>
  );
};

export default Container;
```

## [Posting Reference]
[styled-components Documentation](https://styled-components.com/docs)  
[8 reasons to use styled-components](https://blog.logrocket.com/8-reasons-to-use-styled-components-cf3788f0bb4d/)  
[Implementing Dark Mode In React Apps Using styled-components](https://www.smashingmagazine.com/2020/04/dark-mode-react-apps-styled-components/)  
[How to use media queries with styled components](https://jsramblings.com/how-to-use-media-queries-with-styled-components/)  
