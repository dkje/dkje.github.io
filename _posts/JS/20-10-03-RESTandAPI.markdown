---
layout: post
title: "REST와 API Docs 읽기"
subtitle: ""
date: 2020-10-03 23:15:00
author: "Dev X"
header-img: "img/post-bg/post-bg-rest.png"
published: true
catalog: true
tags:
  - API
  - REST
  - HTTP
---

이 글은 프론트의 입장에서 REST API를 이해하기 위한 목적으로 정리되었습니다.

## 📑API란

**API**는 **Application Programming Interface**의 약자입니다. 언뜻보기엔 어려운 개념 같지만, 차근 차근 단어의 의미를 짚어 해석하면 아래와 같은 뜻을 가집니다.

- **Application Programing**: 응용 프로그래밍
- **Interface**: 두 시스템이 정보를 주고 받는 접점.

즉, <u>두 개의 응용 프로그래밍이 정보를 주고 받을 수 있도록 도와주는 중간 매개체</u>를 API라고 합니다. 외부에서 제공하는 API를 적시적소에 사용하는 것은 개발 속도 향상에 도움이 됩니다. 사실 우리는 **Web API**인 DOM과 Timer, Fetch등을 이미 편리하게 사용해왔습니다.

### API 명세

![표준규격예시](https://i.ibb.co/31t65T9/test.jpg)

<center>API 규격을 지켜야하는 이유</center>
<br/><br/>
**API**는 시스템간 의사소통 위해 지켜야 할 **규격**을 정리해둔 **명세**를 갖고 있습니다. 우리에게 친밀한 **DOM**의 API 명세를 보겠습니다.

[**DOM API 명세서**](https://dom.spec.whatwg.org/#interface-event)

DOM은 **스크립트(프로그래밍 언어)**와 **페이지(HTML,XML)**의 연결과 편의를 위한 기능을 제공하는 API입니다. 위의 링크에 접속하면 DOM이 제공하는 다양한 인터페이스가 정리되어있습니다. 각 항목은 API의 **개념**과 **구성**, 제공하는 **메서드**, **속성** 등을 설명합니다.

예를 들어 **EventListener**를 사용하고 싶다면 <u>2.1 Introduction to "DOM Events"</u>에 정리된 명세를 따라야합니다.

```js
addEventLisnter(anyFunc, "whenIwant");
//명세를 지키지 않은 코드
domObj.addEventListener("load", imgFetched);
// 명세를 지킨 코드
```

1. Dom Object의 메서드이다
2. 첫번째 파라미터로 이벤트를 받는다
3. 두번째 파라미터로 콜백 함수를 받는다

위의 규격을 지키기 않고 단순히 메서드 명만 맞게 작성한다고 코드가 실행되진 않습니다. <u>API 명세를 지키지 않은 코드는 에러를 발생</u>시킬 것입니다.

## 📬REST API

### REST란?

![REST API의 구조](https://i.ibb.co/tpJFZ87/1.png)

**REST**는 Representational State Transfer의 약자로, 웹 상에서 쉽게 통신 할 수 있도록 시스템 간 표준을 제공하는 아키텍쳐 스타일입니다. Representational State가 갖고 있는 <u>상태를 표현한다</u>라는 의미와 맞게 REST는 <u>URI를 통해 요청을 표현할 수 있다</u>는 특징을 갖고 있습니다.

서버와 클라이언트 통신을 구현하다보면 REST가 들어가는 용어들을 자주 만나게 되는데, 용어들은 엇비슷하지만 각각 이런 뜻을 갖고 있습니다.

- **REST**: 시스템 구조 스타일
- **RESTful**하다, **RESTful** 시스템 : REST와 호환되도록 만들어진 시스템을 뜻하는 말
- **REST API**: REST 구조로 제공되는 통신 인터페이스

#### REST의 원칙

1. **클라이언트 서버 분리**: 인터페이스와 DB를 일관적으로 분리했기 때문에 넓은 확장성을 갖고 있습니다.
2. **상태 비저장**: Request에는 요청에 필요한 모든 정보가 포함되어있기 때문에, 콘텍스트가 서버에 저장되지 않습니다. 세션 정보는 클라이언트에서 관리됩니다.
3. **캐시 처리 가능**: Response의 데이터를 캐시할 수 있습니다. 응답의 캐시 가능 여부는 명시적으로 이루어지며, 가능할 경우 동등한 Request에 대해 재사용 권한이 부여됩니다.
4. **인터페이스 일관성**: 아키텍처를 단순화하고, 분리함으로 클라이언트와 서버가 독립적으로 개선되게 합니다.
5. **계층화**: 시스템은 각 요소들의 동작에 제한을 둬 계층적으로 구성되며, 클라이언트는 대상 서버에 직접 연결되었는지 중간 서버를 통해 연결되었는지 알 수 없습니다.

#### Endpoint

![URL의 구조](https://i.ibb.co/rpBSwBM/1.png)

**Endpoint**는 <u>자원(Resource)에 접근하기 위한 경로</u>입니다. 도메인 URL 뒤에 / 키워드와 함께 시작되며, 경로명에 제한은 없지만 자원을 표현 할 수 있는 이름을 짓는 약속이 있습니다.

또 **Endpoint**는 앞에서부터 **계층 관계**를 나타냅니다. 예를 들어 /school이라는 엔드포인트 뒤에 다른 경로가 이어진다면 /student 또는 /teacher등의 순서가 적절합니다. /student/school은 계층 구조가 역으로 이어지기 때문에 상태 표현적인 REST API의 취지와 맞지 않습니다.

```
/member/:id/profile
```

API 명세에 **:**키워드와 함께 쓰인 Endpoint는 **변수**를 나타내는 값이며, Request 요청 시 변수명에 대응되는 값으로 대체해 사용해야 합니다. 예를 들어 나의 아이디가 user1234라면 위의 경로는 '/member/user1234/profile'로 대체됩니다.

```
?keyword=cat&sort=popular
```

**Query String**는 URL을 통해 <u>데이터를 전달하는 방법</u>입니다. **?** 키워드와 함께 시작되며 **파라미터명 = 파라미터 값**의 형태로 쓰입니다. REST에 포함되는 내용은 아니지만 API 명세에 함께 명시되곤 합니다. API 명세에 **Parameters**라는 항목이 있다면 Query String 형식으로 사용해야합니다.

#### Method

REST는 **HTTP Method** 중 POST,GET,PUT,DELETE를 사용합니다.

- **POST**(Create): 새로운 리소스의 생성을 요청합니다.
- **GET**(Read): 리소스를 요청합니다. (데이터를 받기만 합니다)
- **PUT**(Update): 특정 리소스의 수정을 요청합니다.
- **DELETE**(Delete): 특정 리소스의 삭제를 요청합니다.

**Method**는 **Endpoint**, **Query**와 조합되어 의미를 갖습니다. 예를 들어 동일한 Endpoint으로 Request를 보내더라도 POST 메소드를 사용했다면 새로운 리소스가 추가되고, GET을 요청했다면 적합한 데이터를 답신받게 됩니다.

## HTTP Message

![HTTP Message의 구조](https://mdn.mozillademos.org/files/13827/HTTPMsgStructure2.png)

**REST**가 시스템 아키텍쳐라면 **HTTP**는 <u>웹 환경에서 문서를 전송할 때 사용되는 표준 규약</u>입니다. REST API의 대부분이 HTTP로 처리됩니다. HTTP 메시지는 통신 요청이 이루어진 시간부터, 사용자의 어플리케이션 환경까지 다양한 정보를 담고 있습니다. 통신이 잘 이루어졌는지 확인하기 위해서는 네트워크 탭의 HTTP Message를 해석 할 수 있어야 합니다.

**Request**와 **Response**의 메시지 구조는 유사하지만 약간의 차이가 있습니다. 각 메시지의 공통적인 부분을 살펴보자면 아래와 같습니다.

- **First Line**: 요청 또는 요청 처리에 대한 정보
- **Headers**: 요청에 대한 부수적인 정보와 메시지에 대한 설명
- **Blank**: 헤더와 바디를 구분하기 위한 공백
- **Body**: 요청 또는 요청 처리에 대한 내용

### 📨Request의 구조

![Request의 구조](https://i.ibb.co/Dfrjd9F/1.png)

<center>
Request는 StartLine과 Headers, Body로 구성되어있습니다.
</center>

#### 1.Start Line

```
HTTP Method / Origin || Absolute Path / HTTP Version

/////////예시/////////
POST / index.html HTTP/1.1
```

시작 줄은 **HTTP 메서드**, **요청이 이루어진 경로**, **HTTP 버전**에 대한 정보로 이루어져 있습니다. HTTP 메시지의 가장 첫 줄에 위치하며 한 줄로 이루어집니다.

#### 2.Headers

**Header**는 **Request**뿐만 아니라 **Response**에서도 <u>'메시지 컨텐츠와 관련 없는 부수적 정보를 제공'</u>하기 위해 사용됩니다.

- **General headers**: Request, Response 모두에 적용되는 정보.
- **Request headers**: 클라이언트 또는 전달될 데이터에 대한 요청 정보.
- **Response headers**: 서버 또는 path에 대한 응답 정보.
- **Entity headers**: 메시지 바디의 컨텐츠에 대한 정보. Body가 없다면 생략됩니다.

```
Name(대소문자_구분하지_않음):Value
```

header는 문자로 된 **이름**(Name)과 **값**(Value)의 쌍으로 구성됩니다. Request시 기본적으로 생성되는 Header 정보가 있고, 필요에 따라 명시적으로 작성해야하는 Header도 있습니다.

**Request Message**의 Headers는 **General Headers**, **Request Headers**, **EntitiyHeaders**의 세 가지 종류로 구성됩니다. 흔히 보게되는 대표적인 Request Header 종류입니다.

- **General Headers**

  - **Request Method**: 요청에 사용된 메서드(POST,GET,UPDATE,DELETE)에 대한 정보입니다.
  - **Status Code**: HTTP 요청이 성공적으로 완료되었는지에 대한 정보입니다.
    <br/><br/>

- **Request Headers**

  - **Authorization**: 사용자 증명에 대한 인증토큰입니다.
  - **Origin**: 요청을 보낸 도메인을 정보입니다. 요청을 받는 곳과 주소가 다르다면 CORS 정책에 따라 거절 할 수도 있습니다.
  - **Cookie**: 클라이언트에 저장된 쿠키를 키와 값의 형태로 전달합니다.
  - **Accept**: 응답을 원하는 데이터 타입 정보입니다.
  - **User-Agent**: 요청이 이루어진 클라이언트(브라우저)에 대한 정보를 명시합니다.
  - **Cache-Control**: 개인 캐시에 대한 사용 여부를 명시합니다. (Request와 Response 모두에서 사용합니다.)
    <br/><br/>

- **Entity Headers**

  - **Content-Type:** : 요청 리소스의 media type을 명시합니다.

참고: [MDN HTTP Header 문서](https://developer.mozilla.org/ko/docs/Web/HTTP/Headers)

#### 3.Body(Data)

**Body**는 공백 이후에 시작되며, Message의 가장 마지막 요소입니다. 서버에게 전송하기 위한 정보를 작성하지만 <u>필수 요소가 아니기 때문에 Method에 따라 선택적</u>으로 사용됩니다. 전송되는 데이터가 없다면 Body는 비어있게 됩니다.

### 📩Response의 구조

![Response의 구조](https://i.ibb.co/ykyzZmd/2.png)

#### 1.Status Line

```
HTTP Version / Status Code / Status Text
///////예시//////
HTTP/1.1 200 OK
```

응답 줄은 **HTTP의 버전**(일반적으로 1.1), 요청의 **상태 코드**, **상태 텍스트**의 구조로 이루어집니다. Request의 Start Line처럼 Message의 가장 첫 줄에 위치하며 한 줄로 이루어져있습니다.

#### 2.Headers

**Response**의 **Headers** 또한 Request와 동일한 구조로 이루어져 있습니다. **Request Message**에서 볼 수 있는 추가적인 헤더들의 예시입니다.

- **Response Headers**

  - **Set-Cookie**: 쿠기 저장을 명령하는 헤더입니다.
  - **Age**: 캐시가 생성된 후 지난 시간을 초단위로 명시합니다.
  - **Content-Type**: 컨텐츠 타입(MIME)과 인코딩 정보를 명시합니다.

- **Entity Headers**

  - **Content-Type:** : 응답 리소스의 media type을 명시합니다.

#### 3.Body(Data)

Request와 마찬가지로 공백 이후, Message의 가장 마지막에 위치합니다. 필수 요소가 아니기 때문에 POST의 성공 응답으로 돌아오는 201 Status과 PUT의 성공응답으로 돌아오는 204 Status의 경우, 일반적으로 Body가 비어있습니다.

## REST API 문서읽기

REST API에 대해 공부한 것을 기반으로한 REST API사용 예제입니다. 사용할 API는 무료 이미지를 지원해주는 **Unsplash Developers**이며 **PostMan**을 통해 요청과 응답의 결과를 보겠습니다.

### 1. 가입과 앱 등록

![가입예시](https://i.ibb.co/0GPC20Q/image.png)

- [Unsplash Developers](https://unsplash.com/developers)에 접속해 개발자 등록을 합니다.
- Your apps 메뉴를 눌러 앱을 추가합니다.
- 등록한 앱 정보의 Keys에서 Access Key를 사용자 인증 토큰으로 사용하게 됩니다.

### 2. Documentation 읽기

#### Schema

**API Documentation**에서 API를 사용하기 위한 정보를 확인 할 수 있습니다. [Schema](https://unsplash.com/documentation#location)에는 기본적인 정보가 명세되어 있기 때문에 반드시 확인해야 합니다.

- **Location**: Endpoint와 Query를 제외한 Base URL입니다.
- **Version**: 응답으로 받게될 API 버전 정보입니다.
- **Summary objects**: 응답 데이터 정보에 대한 설명입니다.
- **HTTP Verbs**: 사용가능한 요청 메서드 정보입니다.

#### Authorization

**사용자 인증**과 관련된 정보입니다. 가입과 앱 등록에서 받은 **인증 토큰**은 두가지 방식으로 사용 가능합니다.

- **headers**에 추가하기: Authorization: Client-ID YOUR_ACCESS_KEY
- **query**로 추가하기: https://api.unsplash.com/photos/?client_id=YOUR_ACCESS_KEY

### 3. Search 요청 보내기

![search api예시](https://i.ibb.co/wRYQZwC/image.png)

[Search Photo](https://unsplash.com/documentation#search-photos) 요청을 사용해보겠습니다. Document의 Search 항목을 보면 요청에 필요한 정보가 자세히 설명되어 있습니다.

- **HTTP Method**: GET
- **Endpoint**: /search/photos
- **Parameter**: ...

![postman 예시](https://i.ibb.co/LgF0YB9/image.png)

이제 Postman을 통해 검색 요청을 보내보겠습니다.

- **URL**: **Base URL**과 **Endpoint**을 합친 URL로 요청을 보냅니다.
- **Params**: **Query**로는 **query**와 **color** 두가지를 사용합니다. value로 사용 할 수 있는 값에 대한 정보는 docs에 정리되어있습니다. query는 자유로운 string 값이지만 color는 제한적인 범위 내에서 선택해 사용해야합니다. Query값은 Endpoint 뒤에 추가됩니다.
- **Authorization**: Query에 추가하는 방법과 Header에 추가하는 방식 두 가지 중 **Header**에 추가하는 방식입니다. Authorization 탭에서 **API Key** 타입을 체크하고 Key로는 'Authorization', Value에는 'Client-ID AccessKey'를 입력합니다.

![응답 예시](https://i.ibb.co/89Hh8bN/1.png)

이렇게 요청을 보내면 응답이 돌아온 것을 확인 할 수 있습니다.

- **Status**: 200 OK (성공적으로 요청이 이루어짐)
- **Body**: 요청에 따라 처리된 응답 데이터 (JSON 형태로 반환됨)
- **Headers**: 응답에 대한 정보

## [Posting Reference]

[CS Visualized: CORS](https://dev.to/lydiahallie/cs-visualized-cors-5b8h)  
[Fetch API](https://www.zerocho.com/category/HTML&DOM/post/595b4bc97cafe885540c0c1c)  
[HTTP 메시지](https://developer.mozilla.org/ko/docs/Web/HTTP/Messages)  
[HTTP Tutorial](https://www.tutorialspoint.com/http/index.htm)  
[Understanding And Using REST APIs](https://www.smashingmagazine.com/2018/01/understanding-using-rest-api/)  
[Reading API docs](https://learn.parabola.io/docs/reading-api-docs)  
[How To Use an API (The Complete Guide) [A Simple API Example]](https://rapidapi.com/blog/how-to-use-an-api/)  
[What is REST?](https://www.codecademy.com/articles/what-is-rest)  
[REST API 제대로 알고 사용하기](https://meetup.toast.com/posts/92)  
[REST API Tutorial](https://restfulapi.net/)
