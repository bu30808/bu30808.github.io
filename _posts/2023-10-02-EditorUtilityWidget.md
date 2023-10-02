---
layout: post
categories: blog
title: UE5) 에디터 유틸리티 위젯
date: 2023-08-27
tags: [UnrealEngine,Unreal,UE5,EditorUtilityWidget]
toc:  true
---

# 에디터 유틸리티 위젯

지금 개인적으로 진행중인 프로젝트에서, 

2차원배열에 특정 데이터들을 저장할 필요가 생겼는데,

내가 저장해야 할 데이터는 다음과 같다.

![ex_screenshot](/assets/images/unreal/myProejct/orbSystem.png)

이걸 코드로 하나씩 저장한다? 미친짓인거 같아 에디터 유틸리티 위젯을 이용해 툴을 만들기로 했다.

## 에디터 모듈 생성

에디터 유틸리티 위젯을 코드로 생성해서 사용하기 위해서는 먼저 에디터 모듈을 생성해서 붙여줘야 한다.

[에디터 모듈 추가하는 방법](https://unrealcommunity.wiki/creating-an-editor-module-x64nt5g3)

문서가 영어라서 못읽겠다고? 크롬에 붙어있는 자동번역 써도 어느정도 이해하고 충분히 할 수 있다.


## 에디터 유틸리티 위젯 생성

평소처럼 새 C++ 클래스를 추가하고, 부모를 EditorUtilityWidget으로 설정해준다.

여기서 중요한 점은 **타겟 모듈을 반드시 위에서 생성했던 에디터 모듈로 설정해야 한다는 것.**

기본으로 설정된 게임 모듈로 설정하면, 클래스는 생성되지만, 블루프린트로 생성한 에디터 유틸리티 UMG에서 부모를 변경할 때 보이지 않으니 주의


## 에디터 유틸리티 위젯 프로그래밍

그 이후에는 기존 Widget 만들듯이 변수선언하고 똑같이 사용하면 된다.

다만, 

* 내가 수정할 블루프린트 에셋을 들고오거나
* 에디터 유틸리티에서 직접 블루프린트 에셋을 생성하거나
* 이렇게 수정하거나 생성된 블루프린트 에셋을 저장하거나

할 필요가 있을때 사용할 예시 코드를 남긴다.


### 블루프린트 오브젝트 가져오기
<script src="https://gist.github.com/bu30808/fe7e91d3ce820436eb8e7c46aaacb8d1.js"></script>

### 에디터 유틸리티에서 수정한 블루프린트 에셋 저장하기
<script src="https://gist.github.com/bu30808/b4293f6cc8ae5b7d8606ee959b0a4489.js"></script>

### 코드로 블루프린트 오브젝트 생성하기

요건 [유투브 링크](https://www.youtube.com/watch?v=eEy2v95pDWw)


