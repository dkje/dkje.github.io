---
layout: post
title: "모르는 것만 골라서 해본 썰"
subtitle: "사실 정말 시간내에 해낼 수 있을까 잠이 오질 않았다..."
date: 2021-01-01 15:46:00
author: "Dev X"
header-img: "img/post-bg/final-project.png"
published: true
catalog: true
tags:
  - React
  - GraphQL
  - Typescript
  - Apollo Client
---

코드스테이츠에서 진행한 파이널 프로젝트에 대한 회고 글입니다😀<br/>
[이 링크](https://www.notion.so/c9d48cef3ca0489284829f4b9632a5c4)는 팀원들과 나눈 회고담입니다<br/>
배포된 프로젝트는 [여기](https://retrievo.io/)서 확인하실 수 있습니다

## 🚲새로운 기술 배우기

사실 저희 팀은 첫 번째 프로젝트를 마치기 전부터 다음 프로젝트에서 공부하고 싶었던 것들이 있었습니다😅 프로젝트의 스펙은 전에 배우지 못했던 것들을 추가적으로 공부하는 방향으로 정해졌고 큰 단위로 아래와 같이 나뉘어졌습니다.

1. Graphql Api 구현
   : GraphQL의 편의성을 확인하고, 개발 속도에 얼마나 영향을 주는지 알고 싶었습니다.

2. Typescript 이용한 협업
   : 다같이 Typescript를 공부한 적은 있었으나 프로젝트에 적용해보고 싶었습니다.

3. UI 라이브러리 사용
   : 직접 컴포넌트를 구현했을때와 작업 속도를 비교해보고 싶었습니다.

4. CI/CD 약식 구현
   : 테스트 자동화가 프로젝트 개발에 얼마나 편의를 제공하는지 체험해보고 싶었습니다.

5. Jira와 Slack을 Git 연동하기
   : Git만으로 프로젝트를 관리한 경험이 있었기 때문에 다른 협업 툴을 시도해보고 싶었습니다.

사실 이것 외에도 docker나 nest.js등 더 공부하고 싶었던 것이 많았지만, 한 달이라는 시간 안에 프로젝트를 완성하는 것도 고려해야 했기 때문에, 몇가지는 다음을 기약해야 했었습니다😂

## 📈러닝 커브

프로젝트를 시작하며, SR과 기술 공부를 위한 일주일의 시간을 할당했습니다. 가장 큰 걱정은 Typescript였지만 되돌아보면 Apollo를 공부하는데 더 많은 시간을 썼던것 같습니다😅 학습 기간 동안은 아래와 같이 논의가 이루어졌습니다.

1. 공식 문서를 읽고 스택이 우리의 목적에 적합한지 재 확인한다.
2. 강의 또는 웹서칭을 통해 스택의 실 사용 예를 찾는다.
3. 여러 스택을 혼합해 사용 했을 때 발생하는 문제점(주로 깃 이슈)이 있는지 확인한다.
4. 기술 난이도와 시간 비용을 가늠한다.

이 과정에서 큰 맥락으로 있었던 토론을 요약하자면 아래와 같습니다.

1. GraphQL vs Redux
   : 정확하게 정리하자면, Redux와 GraphQL을 함께 사용 할 것인지에 대한 논의였습니다. 지난 프로젝트에서 Context만으로 상태관리를 구현했기 때문에 Redux를 사용하지 않았었고, 취업을 위해서는 Redux를 한 번쯤 사용하는 것이 좋지 않을까?하는 고민이 있었습니다. 하지만 대부분의 GraphQL Client 라이브러리는 fetch한 data들의 상태 관리를 지원합니다. 예를 들면 저희가 사용한 Apollo는 데이터 정규화와 중앙 상태 관리를 cache로 처리하기 때문에, Redux를 사용하게 되면 데이터를 중복 저장하게 됩니다. 결국 스팩을 위해 불필요한 Redux를 사용하는 것은 맥락에 맞지 않는다는 결론을 내렸습니다.

2. Apollo vs Relay, urql
   : GraphQL의 네트워킹과 캐싱을 위해 라이브러리를 사용하기로 합의 후, 어떤 라이브러리를 사용 할 지가 고민이었습니다. 대표적인 세 라이브러리를 두고 비교했는데 모아진 의견은 아래와 같습니다.

   **Relay**: 성능 최적화라는 장점이 매력적이었지만, 학습 시간이 길기 때문에 다른 기술들과 함께 공부할 수 있는 여유가 없을 것 같았습니다.

   **urql**: 다른 라이브러리에 비해 용량이 경량화 되었다는 장점이 있었지만, 최신 라이브러리이기 때문에 트러블 슈팅 시 참고할 만한 레퍼런스가 부족 할 수 있는 위험이 있었습니다.

   그에 비해 **Apollo Client**는 학습 난이도가 낮은 편이었고, 공식 문서와 채널의 튜토리얼이 다양했기 때문에 접근성이 좋았습니다. 또, 프로젝트의 확장 속도가 빠르고 React 외에 Android나 ios도 지원하는 것도 다른 이유였습니다.

## ✨GraphQL

GraphQL API를 구현하며 REST API와는 다른 특성 때문에 많은 우여곡절이 있었습니다😀 그 중 가장 기억에 남는 사건을 요약해보겠습니다.

저희 앱은 대부분의 API에서 ProjectID를 전달받아야만 했습니다. 처음엔 session을 사용할까 했었지만, 유저가 프로젝트를 즐겨찾기 해 접근하는 경우를 위해 url에서 projectId를 특정 지어야했기 때문에 다른 방식을 생각해내야 했습니다. **REST**였다면 단순히 url param으로 ProjectId를 전달했겠지만, **GraphQL**은 resquest에 담아 보내는 data를 arguments을 통해 전달받습니다. 사실 arguments로 projectId를 전달한다고 해서 문제될 것은 없었습니다. 하지만 Rest API에 익숙했던 저에겐 그 친숙함의 틀을 깨는 과정이 신선하게 다가왔습니다.

또 REST API를 사용했다면 여러개로 나뉘어졌을 API가 graphQL에선 하나로 해결되는 일이 많았습니다. 예를 들면 User의 data를 반환하는 Resolver를 구현 할 때, User와 관련된 모든 schema를 relation 시켜두면, Client에선 원하는 data만 요청해 받아갈 수 있었습니다. 그 외에 api document겸 test를 할 수 있는 playground가 자동 지원되는 덕에 많은 작업 시간을 단축 할 수 있었습니다.

_**그럼에도 불구하고...**_ 결국 REST api를 혼용했습니다😅 google과 git의 auth callback이 REST api를 사용하기 때문이었죠. 구현에 큰 문제가 되진 않았습니다만, REST api와 함께 쌓여온 라이브러리 생태계에서 graphQL의 자리가 아직 비좁아 슬픈 현실이었습니다😂

시행착오와 학습 기간 탓에 서버 구현이 빨리 끝났다고 하긴 어려웠지만, 그 고생 값은 Client 작업을 시작하며 빛을 보았습니다😎 api type system을 다져놓은 덕에, code intelligence가 parameter와 response type을 알려주니 api document를 확인 할 일이 적었습니다. 또 추가적으로 api가 필요한 대부분의 경우가 기존 api에 구현해둔 relation system에서 해결되었습니다.

무엇보다 가장 걱정스러웠던 Apollo Client의 cache state 관리를 직접 사용해보며, 이걸 redux로 관리했으면..이라는 대화도 종종 나누었죠. 물론 어려운 점도 있었지만요. cache normalize 시스템을 제대로 이해하지 못하고 서버부터 작업했던 탓에, 데이터 정규화가 제대로 이루어지지 않아 server api를 수정해야 하는 일이 생기기도 했습니다😫

서버부터 프론트까지 graphQL API를 직접 구현해본 후기는 graphQL은 확실히 Client 친화적인 API라는 것입니다. query문과 친숙해지는 과정이 필요했지만 크게 학습이 어려운 문법이 아니었고, 상황에 따라 원하는 data만 응답받을 수 있는 편의성이 더 크게 느껴졌습니다.

그리고 또 재미있었던건 프론트엔드 개발자 또한 DB 구조를 짐작 할 수 있게 된다는 점입니다. query의 depth가 table의 relation과 관련있기 때문에 종종 query를 짜기 위해 db diagram을 열어보기도 했습니다😁 팀 전원이 풀스텍이었던 지라 당시에는 별 생각 없었지만, 곱씹어보니 서버 개발자와 의사소통이 더욱 중요하겠구나를 느낀 부분이기도 합니다.

## 🏒Git Hook

git에서 저지른 실수들은 대부분 수습이 가능하지만, 일단 실수를 안하는 것이 가장 좋겠죠? 이번 프로젝트의 자동화 프로세스 중 제가 담당했던 부분은 Git hook을 이용해 git과 관련한 실수들을 줄이고, 가능한 편의를 제공하는 것이었습니다.

1. upstream remote 또는 master branch로 commit, push 금지
   : reset으로 해결되는 문제긴 하지만, 그래도 가장 잦게 일어났던 실수를 방지했습니다. 특히 commit mesaage를 잘못 쌓고 push 직전 눈치채 reset하는 경우 깊게 느꼈던 자괴감을 방지 할 수 있어 좋았습니다😅 구현 후 가장 보람찼던 기능이었습니다.

2. Eslint 테스트와 prettier 포맷팅
   : cache 기능을 통해 변경이 일어난 file에 한해서만 test를 진행합니다. Eslint extension을 통해 대부분 확인되기 때문에 크게 필요가 있을까 싶었지만, 팀원 분 중 한 분이 잠시 Eslint formatter가 제대로 작동하지 않는 문제가 생긴 것을 테스트를 통해 발견한 경험이 있다고 피드백 주셨었습니다.

3. Commit message 자동화하기
   : Jira의 Task와 Git을 연동하기 위해서는 Commit message에 Task의 Id를 포함해야하는 규칙이 있었습니다. 예를 들면 RE-123과 같은 문자열을 commit message에 추가하면 Task에서 해당 문자열이 포함된 commit들을 모아 볼 수 있는 기능이었습니다. 하지만 매번 Task Id를 확인하는 것과 일일이 Id를 추가하는 것은 번거로운 작업이기 때문에 Branch 이름에 TaskId가 있다면, commit message에 Id가 자동적으로 추가되도록 했습니다.

---

#### 그 외의 감상

이번 프로젝트는 새로운 기술을 많이 학습해야했기 때문에 팀원들과 많은 대화와 의견을 나누었습니다. 서로 배운 것을 나누고 함께 고민하는 것이 혼자 공부하는 것 보다 훨씬 좋은 시너지를 낸다는 것을 느꼈습니다. 때론 열띈 토론을 진행하면서도 서로 존중하는 자세를 놓지않는 모습을 보며 기술 외에도 배운 것이 정말 많은 시간이었습니다. 어쩌면 길게 느껴졌을지 모를 한달이란 시간동안 불철주야 함께 달렸던 팀원 분들께 진심으로 감사드립니다😎

#### [Reference]

[GraphQL은 어떻게 Redux를 대체하는가](https://velog.io/@minsangk/%EB%B2%88%EC%97%AD-GraphQL%EC%9D%80-%EC%96%B4%EB%96%BB%EA%B2%8C-Redux%EB%A5%BC-%EB%8C%80%EC%B2%B4%ED%95%98%EB%8A%94%EA%B0%80-cijz6lfvf4) <br/>
[Apollo Client는 Redux와 무엇이 다른가](https://d2.naver.com/helloworld/4245995) <br/>
[Fullstack React GraphQL TypeScript Tutorial](https://www.youtube.com/watch?v=I6ypD7qv3Z8&t=10121s&ab_channel=BenAwad) <br/>
[urql vs Apollo vs Relay](https://www.howtographql.com/react-urql/0-introduction/)
