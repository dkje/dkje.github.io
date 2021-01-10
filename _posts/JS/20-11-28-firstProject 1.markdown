---
layout: post
title: "Firebase와 React로 웹게임 만든 썰 1"
subtitle: "Atomic design과 styled-component"
date: 2020-11-28 23:28:00
author: "Dev X"
header-img: "img/post-bg/whatsDat.png"
catalog: true
tags:
  - Atomic Design
  - Firebase
  - Context
  - React
  - Project
---

코드스테이츠에서 진행한 첫 번째 프로젝트를 마무리하며 정리하는 회고 글 입니다<br/>
Deploy된 게임은 이 주소에서 즐기실 수 있습니다.<br/>
모바일 지원이 되므로 핸드폰으로 즐기시는 것을 추천드립니다😉<br/>
https://whats-dat-dev.web.app/new-game<br/>

[이 링크는 팀원들과 나눈 회고담입니다](https://www.notion.so/What-s-Dat-2-92c0350f3bf94b91900c749dac9a4a51)

#### 🎮언제든 다시 모여 즐길 수 있는 게임을 만들 수 있으면 좋겠다

함께 프로젝트를 진행하는 분들과 아이디어 회의를 진행하던 중 나왔던 이야기였습니다. 좋은 분들을 만나 팀을 꾸리게 되었고, 부트캠프가 끝나고 나서도 추억으로 남을 프로젝트를 만들고 싶었습니다. 그래서 만들기로 한 것이 다같이 즐기는 웹 게임이었습니다.

## Stack

![Stack](https://i.ibb.co/d0nQ79f/whats-Datstack.png)

프로젝트 기간은 10일이었고, SR 준비를 제외하면 실질적으로 코딩이 가능한 기간이 8일 정도라서 최대한 스펙을 담백하게 정하려고 했습니다. 특히 우리 팀은 프론트엔드 4명이 모였던지라, 선택과 집중을 통해 리액트 하나만 진득하게 써보자!라는 것이 공통된 의견이었습니다. 파이어베이스를 사용하기로 한 것도 같은 이유였습니다. 미니 프로젝트를 한다면, 솔로 프로젝트에서도 이용할 수 있는 경험을 쌓고 싶었습니다.

### Atomic Pattern

![Atomic Pattern](https://i.ibb.co/LzHxNMc/33235048-d083dca6-d217-11e7-9aea-9a5ef5ae6fe7.png)

컴포넌트 설계에 전부터 관심있었던 Atomic pattern을 적용하고자 했습니다. 이번 프로젝트에 Atomic pattern을 적용하기로 한 이유는 아래와 같습니다.

1. React Component 모듈 시스템이 Atomic pattern의 목적성과 일맥상통 한다고 생각했습니다.
2. 작은 볼륨에 Atomic pattern이 적합하진 않지만, 미리 패턴 공부를 해보고 싶었습니다.

Atomic pattern은 5단계로 나뉘어지지만 프로젝트의 컴포넌트의 뎁스가 그렇게까지 깊지 않았기 때문에 Atom, Molecules, Templates, Pages의 4 단계로 나누기로 했습니다.

```
/components
  /atoms
    /Button
      /Button.storyies.jsx
      /Button.styled.js
      /Button.jsx
    /Box
    /Divider
    /Header
  /modules
    /Form
    /RankingList
    /Slider
    /InfiniteScroll
  /templates
    /GameResult
    /Home
    /MyPage
    /Register
```

Storybook Repo에서 위의 3단계까지 작업을 마치고, Client Repo로 이동해 page 작업을 시작했습니다.

### styled-components

별도의 UI library를 사용하지 않고 직접 컴포넌트를 구현하기로 마음 먹었기 때문에, props을 통한 style 설정을 어떻게 하면 효율적으로 할 수 있을 지에 대한 토의가 많았습니다. 우여곡절을 겪으며 저희가 정립한 스타일 확장 방식은 아래와 같았습니다.

우선 theme과 reset 설정은 이전의 포스팅을 기반으로 작업했습니다.
[Styled Components 사용기](https://dkje.github.io/2020/10/13/StyledComponents/)

```js
import styled, { css } from "styled-components";

export const Base = styled.div`
  ${({ theme, ...args }) => {
    return css`
      display: ${args.display};
      color: ${theme.color[args.color]};
      background: ${theme.color[args.backgroundColor]};
      width: ${theme.size[args.width]} || ${args.width};
      height: ${theme.size[args.height]} || ${args.height};
      margin: ${theme.margin[args.margin]} || ${args.margin};
      padding: ${theme.padding[args.padding]} || ${args.padding};
      border-radius: ${theme.radius[args.borderRadis]} || ${args.borderRadis};
      ...
      `;
  }}
`;

export default Base;
```

기본적 style들을 custom 할 수 있는 Base Components의 예시입니다. 전달받은 props 중 theme의 속성과 일치하는 것이 있다면(ex: sm, lg, primary, secondary...) theme의 속성을 사용하고 만약 일치하는 것이 없다면(ex: 1rem, 20px, #333, #fff...) 해당 속성을 style에 적용합니다.

이렇게 만든 Base components는 다른 element로 다형화해 사용 할 수 있습니다.

```js
import React from "react";
import Base from "../Base/Base";

export const Button = ({ children, ...props }) => {
  return (
    <Base {...props} as="button">
      {children}
    </Base>
  );
};

export default Button;
```

기본적인 custom style이 Base에서 모두 처리되었기 때문에, 생성하고 싶은 Dom에게만 해당하는 별도의 속성을 이 단계에서 처리하면 작업이 훨씬 수월해집니다😄 defaultValidatorFn을 통해 필수 props을 체크 할 수도 있지만, validation check는 proptypes로 처리했습니다.

Components를 작업하며 정했던 규칙들도 몇가지 있었습니다.

#### styled-components 관련으로 정했던 규칙들

1. **atom**은 classname을 갖지 않는 styled-components로 생성한다. 부모에게서 상속받은 속성을 거스르는 스타일은 지양한다. (ex: align self, position absolute) 고정 크기 값은 최대한 지양한다. (ex: list Item는 width:100%, list에 max width지정)
2. **atom**은 모든 가변 값을 props로 상속 받는다.
3. **variation**은 확장 문법으로 구현한다.
4. **module**의 classname은 해당 module을 block으로 기준해 **BEM**을 적용해 짓는다. 하위 element의 스타일 선언시 **nest** 문법을 적용한다.
5. **template**은 레이아웃과 관련된 style만 갖는다. (ex: flex, grid, sticky, relative)
6. story book 작업 중 global style, theme에 수정이 필요한 경우 팀원들과 상의한다

모든 규칙을 다 지켰다고 말하기는 어렵지만 가이드가 있었던 덕에 그나마 일정한 사용 방식을 가진 Components들을 만들 수 있었습니다.
