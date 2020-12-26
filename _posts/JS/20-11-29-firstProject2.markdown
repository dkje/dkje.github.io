---
layout: post
title: "Firebase와 React로 웹게임 만든 썰 2"
subtitle: "Context로 Global State를 관리하려는 원대한 꿈이 있었는데요 없었습니다"
date: 2020-11-29 23:28:00
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

[Firebase와 React로 웹게임 만든 썰1]()에서 이어집니다.<br/>
그렇게 UI 작업은 마무리 되었습니다. 그 뒤로 우리가 갖고 있던 계획은 이렇습니다.

> React가 Redux를 대체하기 위해 Context를 만들으니,<br/>
> hook과 Context만 있다면 Redux없이 상태관리가 가능 할 것이다!


### 🧾Context 구조도

![context](https://i.ibb.co/W51BMzZ/context.png)


Context를 Store로 사용하기로 하고 발견한 점은 **반드시 Global Store를 사용하지 않아도 된다**는 점입니다. 예를 들면 User와 RoomContext는 라우트 가드를 위해 컴포넌트 최상단에 배치했지만, Game이 진행될때만 사용되는 GameContext는 Context가 필요한 Component의 바로 위에 배치해도 문제가 없었습니다. Redux를 사용했을땐 Global Store가 점점 비대해지고 때때로 불필요해진 data를 유지해야 했던 문제점이 있었습니다. 하지만 Context는 Provider 하위의 Component가 언마운트 될 때 함께 삭제되므로 Store가 점점 무거워지는 것에 대한 부담감을 덜 수 있었습니다.


**GlobalContext.js**<br/>
GlobalContext는 모든 Provider를 통합하는 역할을 합니다.<br/>
서버에서 받아올 데이터를 저장할 Context는 물론 사용 중인 라이브러리에서 필요한 기타 Provider도 함께 정리했습니다.
```js
const GlobalContextProvider = ({ children }) => {
  return (
    <ThemeProvider theme={theme}>
      <GlobalStyle />
      <UserContextProvider>
          <RoomContextProvider>
            <Router>
              {children}
            </Router>
          </RoomContextProvider>
      </UserContextProvider>
    </ThemeProvider>
  );
};

export default GlobalContextProvider;
```

**GameContext.js**<br/>
Context는 fetch가 이루어지는 단위로 분류되었습니다. 액션의 단위가 작았기 때문에 별도의 reducer 처리는 하지 않았습니다.
```js
const GameContext = createContext();
export const useGame = () => {
  return useContext(GameContext);
};

const GameContextProvider = ({ children }) => {
  const [gameLog, setGameLog] = useState();
  const [loading, setLoading] = useState(false);
  const { currentJoinedRoom } = useRoom();

  useEffect(() => {
    if (!currentJoinedRoom) return;

    const getGameLog = async () => {
      ...
  }, []);

  const submitResult = async ({ value }) => {
    ...
  };

  const value = { gameLog, submitResult };
  return (
    <GameContext.Provider value={value}>
      {!loading && children}
    </GameContext.Provider>
  );
};
```
<br/><br/><br/>

### 😱불시에 재랜더링되는 컴포넌트들

컨텍스트 구현까지는 계획대로 구현된 것 같았지만 게임 테스트 과정에서 Context를 global Store로 사용하는 것에 대한 문제가 발견되었습니다. 게임 플레이 중엔 이와 같은 화면이 있었습니다.

![게임 플레이 화면](https://i.ibb.co/gz5rXp6/image.png)

그리고 여럿이 동시에 플레이를 진행하는 도중에 이상하게도

> 안녕하세요<br/>

라고 입력되어야 할 글씨가<br/>

> 안녀ㅇ하ㅅㅔ요<br/>

라고 입력되고 있었습니다.

![실시간 데이터 전달](https://i.ibb.co/GTG5Fvs/touchicon-180.png)
<center>firestore에서 실시간으로 game data를 받아오던 방식을 도식화한 그림입니다.</center>

저희 앱은 firestore의 특정 doc에 update가 일어날 때마다 변경된 data를 local에 전달받는 onSnapshot이라는 기능을 이용하고 있었습니다. 즉, 다른 유저가 게임 doc을 업데이트 할 때마다 trigger가 실행되며, 내 화면에서도 전달받은 data를 setState하고 있었던 것이죠.

화면 로딩에 시간이 걸리는 복잡한 로직이 없었기 때문에 육안상으로 눈치채지 못하고 있었지만 불필요한 재랜터링이 일어나는 상황이었기 때문에 최적화를 위해선 반드시 짚고 넘어가야 할 문제였습니다. 이에 대한 해결 방식으로 궁리한 것이 아래 두 가지 방식입니다.

1. state를 분할해 관리한다(redux를 쓰든 Context 내부의 state를 분리하든..)
2. state를 화면이 재렌더링 되도 괜찮을 상황에만 set한다

시간이 촉박했기 때문에 1의 전략을 취하기엔 손봐야 할 코드가 너무 많았습니다. 저희가 선택한 방식은 setState를 조건에 맞춰 실행하는 2번 전략이었습니다.

```js
if(user의 화면이 재렌더링되도 괜찮은 상황일 때){
  setGameLog(data);
}
```

이 방식의 문제는 localState와 remoteData가 완전히 동기화되지 않는다는 것입니다. 만약 채팅화면이 존재했다면 반드시 update가 일어날때마다 화면이 재렌더 되는 것이 옳았을 것입니다. 차후 그런 기능이 추가된다면 Remote에선 Chat을 위한 doc을 별도로 분리하고 Local에선 해당 Context와 Component를 별도의 트리에서 관리해야겠다는 것이 현재 계획입니다.


### 👋유저 접속 상태 구현하기

![유저 접속상태 구현](https://i.ibb.co/HtCNGD9/1.png)

두 번째 난관이 유저의 접속상태 구현하기였습니다. onSnapshot을 이용해 변동되는 game data를 받는 것까지는 구현이 가능했지만, 브라우저가 종료되었을시 playerList에서 접속 종료한 유저를 삭제하는 것은 불가능 했습니다.

이 문제를 해결하기 위해서는 Firebase가 실시간 데이터 동기화를 위해 제공하는 Realtime Database를 함께 사용해야 했습니다. Realtime database는 클라이언트의 연결이 해제되는 시점을 감지하는 기능을 제공합니다. Realtime database와 Functions를 함께 사용하면 아래와 같은 로직이 구현 가능합니다.

1. Client : 유저가 방에 입장할때 유저 doc에 roomId를 저장시킨다.
2. Realtime Database : 유저의 접속 상태를 감지하기 시작한다.
3. Functions : 유저가 offline 상태가 되면 roomId로 game room을 찾고, playerList에서 삭제한다.
4. Client: 게임이 강제 종료되었다면 유저에게 이를 알린다.

구현 방법을 찾은 후엔 코드로 옮기는데 큰 문제는 없었습니다.


#### 🔥Firebase로 앱 개발한 후기

**편의성**: emulator와 채널 미리보기, 퍼포먼스 모니터링 등 개발자의 편의를 배려한 기능들이 많습니다. 배포 또한 간편하고 빠르며 배포 버전 단위의 롤백을 지원합니다.<br/>

**러닝 커브**: 공식 문서가 친절한 듯 하지만 핵심만 간략하게 추려진 편이라 초보자가 접근하기에 쉬운듯 쉽지 않았습니다. 초기 config 설정이 혼란스러웠습니다. <br/>

**비용**: read와 write 횟수로 가격을 책정합니다. 간단한 포트폴리오 앱이라면 프리티어만으로 과금 없이 사용 할 수 있습니다. <br/>

프론트앤드 개발자라면 firebase를 언젠가 이용하게 될 것이라는 조언을 듣고 도전한 첫 웹앱 개발기였습니다. Client에서 직접 DB에 read, write 요청을 보낼 수 있는 덕분에 많은 작업량을 단축 할 수 있었습니다. 단기간 안에 앱을 개발하는 것에 큰 도움이 되었고, 개인적으로 AWS보다 높은 접근성 덕에 개인 작업을 할때 자주 사용하게 될 것 같습니다.