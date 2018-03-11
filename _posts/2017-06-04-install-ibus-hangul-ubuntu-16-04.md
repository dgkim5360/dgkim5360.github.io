---
layout: post
title: How to Install iBus-Hangul in Ubuntu 16.04
date: 2017-06-04 14:13:00 +0900
category: Linux
tags:
  - Ubuntu 16.04
  - ibus-hangul
  - tutorial
  - installation
---
## 시작하기 앞서
한글 사용자를 위한 글이니 한글로 작성하겠다.

## tl;dr

1. "Language Support"에서 언어팩 업데이트
2. 재부팅
3. "Text Entry Settings"에서 `Korean (Hangul) (iBus)` 추가
4. `super+space`로 입력 소스를 바꿀 수 있게 된다. 태극 문양을 볼 수 있다. 아직 영어 타자임을 확인한다.
5. `shift+space`로 한글 타자가 입력되는지 확인한다. 한자키인 `F9`도 테스트해본다.

## 언어 패키지 업데이트
Ubuntu를 설치하고 처음으로 "Language Support"를 실행시키면 자동으로 업데이트 할 것인지 물어본다.

![language support at Home](/assets/img/ubuntu-ibus-hangul-01-language-support.png)

이 업데이트를 해주자. 업데이트 후 "Languages for menus and windows:" 아래 있는 언어 리스트 중에 "Korean"이 있는지 확인한다.

![language support setting](/assets/img/ubuntu-ibus-hangul-02-setting-language-support.png)

없으면 "Install/Remove Languages..."를 눌러 "Korean"을 추가해주자. 그리고 재부팅을 한다.

## Text entry setting
이제 상태표시줄에서 언어 입력기 (`En`이라고 되어있는 것)를 눌러서 "Text Entry Settings..."를 눌러보자.

`English (US)` 밖에 없을 것이다. `+` 버튼을 눌러서 `Korean (Hangul) (iBus)`를 추가한다. 그리고 모드 변경하는 키는 원하는 것으로 설정하면 된다. `Korean (Hangul) (iBus)`가 없다면, 재부팅을 했는지 체크해보자.

![text entry settings](/assets/img/ubuntu-ibus-hangul-03-text-entry-settings.png)

여기까지 세팅이 완료되면, 이제 설정한 변경 키를 통해 `En` 표시가 아름다운 태극 마크로 변경되는 것을 확인할 수 있다. 하지만 타자를 쳐보면 여전히 영어가 입력되는데, 당연한 것이니 부디 절망하지 말자.

## iBus Hangul Setup
왜 그런 것일까? 지금까지 무슨 일을 한 것인가? 우리는 `Korean (Hangul) (iBus)`라는 input source를 추가했다. `English (US)`라는 input source는 오직 영어만을 제공하고, `Korean (Hangul) (iBus)`는 영어와 한글 및 한자를 제공하는 input source이다. 따라서 우리가 input source를 `English (US)`에서 `Korean (Hangul) (iBus)`로 바꾼 것은 영어에서 한글로 타자를 바꾼 것이 아니라, 영어만 쓸 수 있는 input source에서 한/영을 쓸 수 있는 input source로 바꾼 것이다. 그리고 한/영 중에 영어로 설정되어있기 때문에 우리는 한글이 쳐지길 기대했지만 영어 타자가 나온 것이다.

![ibus menu](/assets/img/ubuntu-ibus-hangul-04-ibus-menu.png)

말이 장황했는데, 간단히 위의 태극 문양을 눌러보면 `hangul mode`를 볼 수 있다. 이 `hangul mode`가 켜져 있지 않으면 영어 타자를, 켜져 있으면 한글 타자를 쓸 수 있다. 그 밑에 setup을 눌러보자

![ibus Hangul setup](/assets/img/ubuntu-ibus-hangul-05-setup.png)

이제 여기서부터는 감이 잡힐 것이라 생각이 된다. 한글 입력과 관련된 다양한 설정을 할 수 있고, 토글 키도 설정할 수 있다. Hanja 탭에서는 한자 변환 키를 설정할 수 있다.

## 정리
모든 토글 키를 변경하지 않고 기본값을 사용하는 내 입장에서 정리해보면 위의 설치 및 설정을 끝내면 다음과 같은 과정을 통해 한글을 입력할 수 있다.

1. 영어 only 모드라면 super+space로 태극 문양을 띄운다.
2. 여전히 영어 타자 모드일 것이므로 shift+space로 한글 모드로 바꾼다.
3. 한글 타자를 쓸 수 있다. F9 키를 통해 한자 또는 특수 문자 변환을 할 수 있다.
