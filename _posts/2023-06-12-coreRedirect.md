---
layout: post
categories: blog
title: UE5) 코어 리디렉트
date: 2023-06-12
tags: [코어리디렉트,CoreRedirect,UnrealEngine,Unreal,UE5]
toc:  true
---


원본문서 https://docs.unrealengine.com/4.26/ko/ProgrammingAndScripting/ProgrammingWithCPP/Assets/CoreRedirects/

--------------------------------

솔직히 원본문서 봐도 뭔지 잘 모르겠음.

테스트를 통해서 한번 알아보겠음.

단순히 말하자면, 코드에서 클래스 이름을 변경해서 컴파일 하거나 하면,
기존에 해당 클래스를 상속받는 블루프린트의 부모참조가 다 깨져서 열 수 없는 상태가 되어버림.
이 깨진 블루프린트를 다시 만들어서 참조중이던 변수에 다 일일히 넣거나 수정하기 매우 귀찮음.

이럴때 사용 가능한것이 코어 리디렉트 기능임.

**폐기되거나 제거된 클래스를 대체시켜주는 기능**


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

