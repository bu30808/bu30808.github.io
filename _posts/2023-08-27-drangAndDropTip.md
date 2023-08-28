---
layout: post
categories: blog
title: UE5) 드래그 앤 드롭의 문제와 해결
date: 2023-08-27
tags: [UnrealEngine,Unreal,UE5,Drag&Drop]
toc:  true
---

언리얼 엔진 공식 문서에 보면, 

드래그 엔 드롭에 대한 예제 및 설명이 아주 친절하게 되어 있어서

그냥 코드로 옮겨서 쓰던가 블루프린트 그대로 배껴서 쓰던가 하면 되는데,

공식 그대로 만들 경우에 생기는 문제점이 하나 있다

## 문제점

미리 메인이 될 위젯에 여러가지 다른 타 위젯들을 생성해놓고 숨김처리 한 뒤,

필요에 따라 Visibility를 변경하는 경우

공식 문서대로 따라하면 항상 드래그 했던 위젯이 가장 맨 앞에 위치하게 된다.

## 이유

원래 드래그할 위젯은 메인위젯 내부 캔버스패널 내부에 있던 위젯이다.

캔버스 패널 내부에 있던 위젯을 끄집어 내서 새로 붙이는 식으로 구현되어 있기 때문에 항상 맨 앞으로 나오는 것이다.

## 해결

매인 위젯의 캔버스를 오퍼레이션에 저장해주고,

드롭할 때, 캔버스의 자식으로 추가해 주면 된다.

```
	  //오퍼레이션으로 가져온 드래그중인 위젯을 잠깐 제거합니다.
			Oper->DraggedWidget->RemoveFromParent();
			//캔버스 패널에 다시 추가합니다.
			CanvasPanel->AddChildToCanvas(Oper->DraggedWidget.Get());
			if(UCanvasPanelSlot* CanvasSlot = Cast<UCanvasPanelSlot>(Oper->DraggedWidget->Slot))
			{
        //크기를 재 설정 합니다.
				CanvasSlot->SetSize(Oper->DesiredSize);
				CanvasSlot->SetPosition(UWidgetLayoutLibrary::GetMousePositionOnViewport(this) - Oper->DragOffset);
			}
```
