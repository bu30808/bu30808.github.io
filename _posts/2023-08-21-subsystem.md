---
layout: post
categories: blog
title: UE5) 싱글톤을 대체하는 서브시스템
date: 2023-08-21
tags: [UnrealEngine,Unreal,UE5,singleton, subsystem]
toc:  true
---

유니티도 사용하다보니, 자연스럽게 싱글톤을 구현해서 사용하고 있었는데,

언리얼엔진 내부에서 싱글톤 클래스를 만들어 사용하는 경우, 

예상치 못한 위치에서 가비지 컬렉팅이 되거나 하는 경우가 생겨 

메모리 참조 에러로 간헐적으로 게임이 빵빵 터지곤 했다.


알고보니 기본적인 언리얼 엔진 오브젝트의 라이프사이클과 맞지 않기 때문이었는데,

이에 서브시스템이라는 클래스를 사용하라고 권장하고 있다.


서브시스템은 라이프사이클이 일치해서 도중에 가비지컬렉팅당해서 터지는 일이 없다.

따로 등록하는 과정 필요없이, 그냥 상속받아서 코드를 만드는 즉시 자동 등록되어 사용되며

1. 에디터 서브시스템
2. 게임 인스턴스 서브시스템
3. 로컬 플레이어 서브시스템

이렇게 세가지 종류가 있다.

1번은 모르겠고, 2번과 3번을 주로 사용했으며

플레이어와 관련되서 싱글톤으로 처리할 경우에는 2번

그렇지 않은 경우에는 3번을 사용했다.


2번 사용법
```
UMyPlayerSubsystem * MySubsystem = LocalPlayer->GetSubsystem<UMyPlayerSubsystem>();
```
LocalPlayer는 플레이어 컨트롤러 내부 변수로 존재하니까 가져다 쓰면 된다.


3번 사용법
```
UMyGameSubsystem* MySubsystem = GameInstance->GetSubsystem<UMyGameSubsystem>();
```
게임 인스턴스는 UGameplayStatics에 전역 함수로 가져올 수 있다.
