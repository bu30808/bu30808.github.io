---
layout: post
categories: blog
title: UE5) 플레이팹 로그인
date: 2023-07-18
tags: [UnrealEngine,Unreal,UE5,PlayFab]
toc:  true
---

## 모듈 추가

플레이팹 플러그인 내부 기능을 코드에다 갖다 쓰려면 일단 모듈부터 추가하자.

```
 "PlayFab", "PlayFabCpp", "PlayFabCommon","JsonUtilities","Json"
```
JSON 모듈도 같이 추가했는데, 플레이팹 내부 DB를 건드릴 때 사용하기 때문에 같이 추가해줬다.

------------------------

## 플레이팹 초기화

플레이팹과 상호작용하기 위해서는 PlayFabClientAPI가 필요하다.

그리고 이 API는 게임이 종료될 때 까지 유효한것 같다(추측임)

그런고로, 어디서인가 ClientAPI를 받아서 가지고 있다가

필요할때마다 접근해서 사용하면 되겠다.

나같은 경우는 플레이어 컨트롤러 내부에 컴포넌트 하나 만들어서 붙였다.


1. PlayFabClient 초기화

```
PlayFabClientPtr PlayFabClient = nullptr;
```
어딘가에 플레이팹 클라이언트를 저장할 변수를 선언한다.



2. 클라이언트 API를 가져와 저장한다.
      
```
if (PlayFabClient.IsValid() == false)
{
		PlayFabClient = IPlayFabModuleInterface::Get().GetClientAPI();
		GetMutableDefault<UPlayFabRuntimeSettings>()->TitleId = APP_ID;
}
```

여기에 들어가는 APP_ID가 타이틀 ID다.

기억이 안난다면 이전 포스트를 참고.

-----------------------

## 이메일을 통한 계정생성

초기화된 ClientAPI를 이용해서 이메일 계정을 생성해보자.

<script src="https://gist.github.com/bu30808/5a3a85fe4b9885a0310b31ecda759f9d.js"></script>

request 구조체 변수에다가,

생성할 계정 메일주소 및 비밀번호, 유저이름을 받아서 저장한 뒤.

플레이팹 클라이언트 내부 RegisterPlayFabUser함수에다가 전달해주면 된다.

플레이팹 클라이언트 내부 함수를 살펴보면 이메일뿐만 아니라 다양한 계정 연동 기능이 있으니 확인해보면 좋을 듯 하다.

그리고 두번째, 세번째 인자로 델리게이트를 받는데

선언을 참고해서 로그인 성공시 혹은 실패시 할 행동을 정의해서 넘겨주면 된다.

### 생성된 계정 확인

위에서 만든 계정이 정말 추가되었는지 확인해보자.

개발자 페이지에 내 타이틀 세부정보에 접속한 뒤, 플레이어 항목으로 들어가자.

![ex_screenshot](/assets/images/unreal/playfab/playerMenu.png)

검색 페이지가 열릴텐데, 아무것도 입력하지 않고 검색을 눌러보면

내가 생성한 계정 정보가 나오고, 이름을 누르면 세부정보를 확인할 수 있다.

위에서 설정한 UserName변수값은 마스터플레이어 계정(우하단)에 PlayFab사용자 이름에 작성되어있다.

-------------------------

## 이메일을 통한 로그인

생성한 계정으로 로그인을 해 보자.

<script src="https://gist.github.com/bu30808/0e32e9e354cad2d92c0c0cc60115c5b8.js"></script>

아까랑 거의 비슷한 구조다.

플레이팹 클라이언트 내부의 특정 함수를 호출하고, 

필요한 파라미터를 생성해서 넘겨주면 된다.

성공적으로 로그인 했다면, 같이 전달한 델리게이트가 호출될 것이고,

실패한 경우에는 전달한 델리게이트에 들어가는 변수(const FPlayFabCppError&) 내부에 에러 코드와 메시지가 있으니 

확인해본 후 조치하면 된다.
