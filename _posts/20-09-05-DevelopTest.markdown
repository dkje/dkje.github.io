---
layout: post
title: "테스트 주도 개발과 테스트의 종류"
subtitle: ""
date: 2020-09-05 21:13:00
author: "Dev X"
header-img: "img/post-bg/post-bg-til.jpg"
catalog: true
published: false
tags:
    - unit test
    - TDD
    -
---

![개발 테스트 밈](https://i.ibb.co/89MXRwT/1z6pb4.jpg)
_<center style="font-size: 2rem">if(테스트를 하지 않는다면) 테스트를 실패하지 않는다</center>_

<span style="color:grey">
유닛 테스트를 하면 좋다~ 테스트 툴은 jest가 유명하다~라는 것까지는 관성적으로 공부를 마쳤습니다. 그러나 막상 jest에 대해 공부를 시작할까하니 도저히 테스트의 목적과 종류를 공부하지 않고는 넘어갈 수가 없었습니다! 이 글은 테스트의 종류와 테스트를 왜 치루어야하는지에 대해 공부하며 정리한 글입니다.
</span>

## 테스트 주도 개발(aka TDD)

1. 테스트 코드를 먼저 작성한다
2. 테스트 코드를 통과할 코드를 작성한다
3. 작성한 코드를 리팩토링한다

**테스트 주도 개발**(Test Driven Development)는 <u>테스트를 먼저 작성한 후 테스트를 통과하는 코드를 작성</u>하는 개발 방식을 말합니다. 코드를 작성한 후 테스트 코드를 작성하는 것과 무슨 차이인가? 싶다면 **Driven**이 들어간 만큼 <u>테스트가 개발을 주도한다</u>는 것에 주목해야합니다.

**TDD의 장점**
유지보수 비용이 낮아진다.
결함을 줄일 수 있다.

**TDD의 단점**
유지보수 비용이 줄어든만큼 개발 비용이 늘어난다.

### Unit Tests

### Integration Tests

### Functional Tests

### Smoke Tests

### End-to-end Tests

## [Posting Reference]

[[번역]쉬운 테스트 주도 개발과 단위 테스트를 위한 5단계 방법론](https://medium.com/@cmygray/%EB%B2%88%EC%97%AD-%EC%89%AC%EC%9A%B4-%ED%85%8C%EC%8A%A4%ED%8A%B8-%EC%A3%BC%EB%8F%84-%EA%B0%9C%EB%B0%9C%EA%B3%BC-%EB%8B%A8%EC%9C%84-%ED%85%8C%EC%8A%A4%ED%8A%B8%EB%A5%BC-%EC%9C%84%ED%95%9C-5%EB%8B%A8%EA%B3%84-%EB%B0%A9%EB%B2%95%EB%A1%A0-b82fea6c8d90)  
[[번역] TDD 변절자: TDD는 설계 기법이 아니다.](https://medium.com/@mjspring/%EB%B2%88%EC%97%AD-tdd-%EB%B3%80%EC%A0%88%EC%9E%90-tdd%EB%8A%94-%EC%84%A4%EA%B3%84-%EA%B8%B0%EB%B2%95%EC%9D%B4-%EC%95%84%EB%8B%88%EB%8B%A4-2519b05474d0)  
[TDD(Test Driven Development) 를 연습하면서 참고하기 좋은 팁 10가지](https://medium.com/@rinae/tdd-test-driven-development-%EB%A5%BC-%EC%97%B0%EC%8A%B5%ED%95%98%EB%A9%B4%EC%84%9C-%EC%B0%B8%EA%B3%A0%ED%95%98%EA%B8%B0-%EC%A2%8B%EC%9D%80-%ED%8C%81-10%EA%B0%80%EC%A7%80-d8cf46ae1806)
