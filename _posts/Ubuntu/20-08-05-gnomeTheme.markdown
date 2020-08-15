---
layout: post
title: '우분투 테마 설정하며 리눅스 명령어랑 친해지기'
subtitle: ' 목표는 맥분투 '
date: 2020-08-05 21:49:00
author: 'Dev X'
header-img: 'img/post-bg/post-bg-ubuntu.png'
catalog: true
tags:
    - ubuntu
    - userTheme
    - gnome
    - 맥분투
---

![세팅을 마친 우분투의 바탕화면](https://i.ibb.co/bmBtgQ3/image.png)

<center>세팅을 마친 깰끔쓰한 우분투(맥컨셉)</center>

우분투 세팅을 시작하며 모르는 명령어를 많이 만나게 되었습니다. 간단한 리눅스 명령어는 알고 있었는데... 순간 쫄아 주춤했지만 인생은 역시 실전이죠! 명령어와 친해질 겸, 기분 전환도 할 겸~ 명령어를 한 줄 한 줄 파헤치며 우분투 테마 설정을 해봅시다!
<br/><br/><br/><br/>

## 1-1. gnome-tweaks 설치하기

우선 테마 설정을 하기 위해선 GNOME(우분투의 기본 데스크탑) 확장 플러그인을 관리하는 **gnome-tweaks**를 설치해야 합니다. 터미널을 켜고 아래의 명령어를 입력합니다.

```
sudo apt install gnome-tweaks
```

**sudo** : substitute user do의 약자입니다. 유닉스 운영체제에선 보안을 위해 root와 사용자 계정 권한을 분리해 사용합니다. sudo명령어는 root 권한을 빌려 이후의 명령어를 실행합니다.  
<span style="color:grey">권한과 관련된 다른 명령어로는 su, whoami, logout, exit 등이 있습니다.</span>

**apt**: apt는 Advanced Package Tool의 약자입니다. apt는 리눅스의 패키지 관리자로 응용 프로그램의 설치, 삭제, 최신 버전 업데이트 등의 작업을 처리합니다. 리눅스 명령어를 검색하다 보면 apt와 apt-get을 보게 되는데, 둘은 같은 기능을 하는 패키지 관리자로 어느 것을 써도 상관없지만 apt가 더 자세한 설치 과정을 보여줍니다.

**install**: apt 관리자를 통해 뒤에 올 패키지를 설치하겠다는 뜻입니다.  
<span style="color:grey">패키지를 관리하는 다른 명령어로 reinstall, upgrade, remove 등도 있습니다.</span>

**gnome-tweaks**: 우리가 설치할 패키지의 이름입니다.

위의 명령어를 모아 보면  '관리자 권한으로 apt 패키지 매니저를 사용해 gnome-tweaks를 설치하겠다'는 뜻이 됩니다. 설치를 마치면 애플리케이션 목록에서 gnome-tweaks를 확인할 수 있습니다.

![gnome아이콘](https://i.ibb.co/SKtmZMj/1.png)

<center><span style="color:grey">(이미 아이콘 테마가 설치되어 있어 모양이 다르지만 같은 애플리케이션입니다.)</span></center>
<br/><br/>

## 1-2. gnome-tweak 기본 기능

![gnome세팅화면](https://i.ibb.co/nLWd1W5/2.png)
gnome-tweak는 기본 애플리케이션에도 우분투 테마를 커스텀할 수 있는 기능이 있습니다. **글꼴** 탭에선 시스템 폰트를 **최상위 표시줄**에선 상단 바의 표시 목록을 설정할 수 있습니다.

그 외에도 많은 기능이 있지만 완벽한 맥컨셉을 만들기 위해 중요한 것은 **확장**과 **모양새** 탭입니다. **모양새** 탭은 우분투의 테마를 설정할 수 있습니다.

20.04의 기본 테마는 Yaru이고 그 외에도 선택지를 눌러보면 여러 가지 테마를 볼 수 있습니다. 그중 마음에 드는 것이 있다면 다른 테마를 추가 설치하지 않아도 되겠지만, 저는 맥과 비슷한 테마를 사용하고 싶습니다! 그러므로 우리는 유저가 만든 테마를 다운로드하여 사용할 것입니다.
<br/><br/><br/><br/>

## 2-1. 유저 테마 확장 프로그램 설치

#### **1) 크롬에서 [gnome-shell-extension](https://chrome.google.com/webstore/detail/gnome-shell-integration/gphhapmejobijbbhgpjhcjognlahblep/related) 을 설치합니다.**

![gnome_확장기능_스크린샷](https://i.ibb.co/hcx8Bx9/3.png)

#### **2) GNOME-EXTENSIONS에서 [User-Themes](https://extensions.gnome.org/extension/19/user-themes/)을 설치합니다.**

![gnome_유저테마_확장기능_스크린샷](https://i.ibb.co/GPxzxgS/4.png)

링크에 접속하면 오른쪽의 토글 메뉴가 OFF로 되어 있을 것입니다. 버튼을 눌러 ON으로 만들어줍시다. (아직 크롬 익스텐션을 설치하지 않았다면 User Theme을 사용할 수 없다는 메시지가 뜰 것입니다.)

#### **3)** **User themes 활성화시켜주기**

![gnome_세팅_스크린샷](https://i.ibb.co/T438sD1/5.png)
gnome-tweaks를 재실행해 확장 탭을 누르면 User themes가 생긴 것을 볼 수 있습니다. 토글 버튼을 눌러 확장 기능을 활성화시켜줍시다!
<br/><br/><br/><br/>

## 2-2. 유저 테마 찾기

#### **1) 마음에 드는 유저 테마 찾기**

유저 테마를 검색하기 위해 [GNOME-LOOK](https://www.gnome-look.org/browse/cat/)에 접속해봅시다. Full Icon Themes와 Cursors, Gnome Shell Themes 등의 카테고리에서 다양한 유저 테마를 만날 수 있습니다. 카테고리가 많지만 그중 몇까지만 살펴보겠습니다.

![카테고리에_해당하는_ui_설명](https://i.ibb.co/ncBCHdM/6.png)

**Full Icon Themes**: 애플리케이션 또는 파일 등의 아이콘 디자인을 변경합니다.  
**Cursors**: 마우스 커서의 디자인을 변경할 수 있습니다.  
**Gnome Shell Theme**: 탑 패널, 시스템 탭 등의 디자인을 변경할 수 있습니다.  
**GTK3 Themes**: 기본 내장 애플리케이션 (파일 관리자, 날씨, 음악)등의 디자인을 변경할 수 있습니다.

각 카테고리에서 마음에 드는 테마를 다운로드 해 압축을 풀어 줍시다.

참고용 제가 사용 중인 테마입니다.

Full Icon Theme : [La Capitaine](https://www.gnome-look.org/p/1148695/)  
Cursors : [McMojave cursors](https://www.gnome-look.org/p/1355701/)  
GTK3 Theme :[Prof-Gnome-theme](https://www.gnome-look.org/p/1334194/)  
Gnome Shell Theme : [WhiteSur Gtk Theme](https://www.gnome-look.org/p/1403328/)

#### **2) 다운로드한 파일 폴더로 이동시키기**

gnome-tweaks는 특정 폴더의 파일을 인식하기 때문에 새 폴더를 생성해야 합니다. 파일 관리자로 새 폴더를 만들어도 되지만 이번엔 명령어로 폴더를 생성해봅시다.

```
mkdir ~/.themes
```

**Gnome Shell Theme**과 **GTK3 Theme**에 해당하는 파일이 위치할 폴더입니다.

```
mkdir ~/.icons
```

**Cursors**와 **Full Icon Theme**에 해당하는 파일이 위치할 폴더입니다.

간단하게 명령어도 살펴보겠습니다

**mkdir** : make directory의 약자입니다. 현재 터미널의 현재 디렉토리를 기준으로 폴더를 생성합니다.
<span style="color:grey">
디렉토리, 파일과 관련된 다른 명령어로는 ls, cd, touch, rm 등이 있습니다.
</span>

**~/.themes** & **~/.icons** : 생성할 폴더명과 위치입니다. /은 디렉토리의 경로를 의미합니다.
/ 앞의 ~는 홈 디렉토리를 뜻합니다. 현재 터미널의 디렉토리 위치와 상관없이 홈 디렉토리를 기준으로 하는 경로입니다. 즉 ~/.themes 는 홈디렉토리의 바로 하위 경로에 .themes 폴더를 생성한다는 뜻입니다.
<br/><br/><br/>

## 2-3. 유저 테마 설정하기

![gnome_세팅의_유저테마설정](https://i.ibb.co/pQ94Mz8/7.png)

gnome-tweaks를 재실행해 모양새 탭을 켭니다. 다운로드한 테마를 압축 풀기해 알맞은 폴더에 옮겨두셨다면 선택지에 유저 테마가 추가된 것을 확인할 수 있습니다. 마무리로 분위기 있는 바탕화면 이미지까지 설정 완료한다면 느낌 있는 우분투 테마 세팅이 완료되었습니다! 짝짝짝!
<br/><br/><br/>

## 번외. 다른 편리한 확장 기능

GNOME의 확장 기능 중엔 gnome-tweaks 외에도 편리한 기능들이 많습니다.
사용을 추천할만한 확장 기능 몇 가지를 더 소개합니다.

![Dash_to_dock_스크린샷](https://i.ibb.co/C83T83c/8.png)

#### [**Dash to Dock**](https://extensions.gnome.org/extension/307/dash-to-dock/)

테마 설정만으로 약간 부족한 맥의 느낌을 보충할 수 있는 확장 도구입니다. 독의 위치와 색상, 아이콘 크기 등의 디자인을 설정할 수 있습니다. (설치 후 gnome-tweaks의 확장탭에서 설정 가능합니다)

![ConverflowAltTab 스크린샷](https://i.ibb.co/XXG134t/9.png)

#### [**ConverflowAltTab**](https://extensions.gnome.org/extension/97/coverflow-alt-tab/)

Alt+Tab을 누르면 현재 활성화 중인 창 목록을 슬라이드로 보여줍니다. Alt 키를 누른 채로 Tab키를 눌렀다 떼면 마우스를 조작하지 않아도 쉽게 화면 변경이 가능합니다. (시작 키를 눌러도 같은 기능을 사용할 수 있지만 못생긴 측면 독바를 보지 않아도 된다는 장점이 있습니다..)

![Clipboard Indicator 스크린샷](https://i.ibb.co/Y3tFxjN/10.png)

#### [**Clipboard Indicator**](https://extensions.gnome.org/extension/779/clipboard-indicator/)

클립보드에 저장했던 내용들을 자동으로 저장해두었다가 꺼내 쓸 수 있습니다

![Open Weather스크린샷](https://i.ibb.co/H4MXtgY/11.png)

#### [**Open Weather**](https://extensions.gnome.org/extension/750/openweather/)

탑 패널에 날씨 정보를 추가합니다. 초기 설정 시엔 외국 기준으로 되어있지만 설정을 통해 위치정보와 섭씨 표시를 변경할 수 있습니다.

![Screenshot Tool](https://i.ibb.co/nP0c3D2/12.png)

#### [**Screenshot Tool**](https://extensions.gnome.org/extension/1112/screenshot-tool/)

스크린샷 기능을 탑 패널에서 관리할 수 있는 툴입니다. 우분투에 기본 스크린샷 단축키가 존재하긴 하지만 캡처된 사진을 탑 패널에서 간단하게 조작할 수 있다는 장점이 있습니다.
<br/><br/><br/><br/>

—— Dev X 2020.08.05

<br/><br/>

\[포스팅 레퍼런스\]
[https://itsfoss.com/install-themes-ubuntu/](https://itsfoss.com/install-themes-ubuntu/)
