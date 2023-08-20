---
layout: post
categories: blog
title: UE5) 위젯의 Visiblity를 변경 후 한번 클릭해야 상호작용되는 경우
date: 2023-08-19
tags: [UnrealEngine,Unreal,UE5,UMG,Widget]
toc:  true
---

인벤토리를 추가하고,

특정 키를 누르면 껐다 켰다 하도록 만들었다.

문제는 인벤토리가 열리고 나서 한번 클릭해줘야 버튼의 호버나 다른 이벤트가 동작하는 것이었다.

이런 경우, 인풋모드를 잠깐 바꿔주면 된다.


인벤토리가 열렸을 때,
```
UWidgetBlueprintLibrary::SetInputMode_GameAndUIEx(GetOwningPlayer(),this);
```
인벤토리 위젯으로 인풋모드를 바꿔주고


닫을 때,
```
UWidgetBlueprintLibrary::SetInputMode_GameAndUIEx(GetOwningPlayer(),nullptr);
```
비워주면 된다.
