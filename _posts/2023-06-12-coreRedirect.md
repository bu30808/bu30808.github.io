---
layout: post
categories: blog
title: CoreRedirect
date: 2023-06-12
tags: [코어리디렉트,CoreRedirect,UnrealEngine,Unreal,UE5]
toc:  true
---


1. 테스트할 TestCharacter라는 클래스를 생성
2. TestCharacter를 상속받는 블루프린트 생성
3. 에디터 닫음.
4. DefaultEngine.ini을 열고 다음과 같이 추가함.

```
[CoreRedirects]
+ClassRedirects=(OldName="/Script/Elfinos_Galileo.TestCharacter",NewName="/Script/Engine.Actor")
```

5. 에디터를 다시 열고 TestCharacter를 상속받던 블루프린트를 열고 부모 확인
6. 블루프린트의 부모가 Actor로 변경된것을 확인 가능.


* 앤진 기본제공 클래스가 아니라 내가 만든 클래스로 변경하려면,

```
+ClassRedirects=(OldName="/Script/모듈이름.클래스이름",NewName="/Script/모듈이름.변경할 클래스 이름")
```

예시)
```
+ClassRedirects=(OldName="/Script/Elfinos_Galileo.TestCharacter",NewName="/Script/Elfinos_Galileo.StructureActor")
```


------------------------------
* 라이더의 리팩토링 기능을 이용할 경우, 자동적으로 리디렉트가 추가됩니다.

