---
layout: post
categories: blog
title: 월드 파티션 기록2
date: 2024-03-25
tags: [UnrealEngine,Unreal,UE5,월드파티션]
toc:  true
---

## 월드 파티션을 사용하며 있었던 내용을 기록한다. 2차

### 멀리 있는 장소에 캐릭터 이동
- 먼저 로드할 필요가 있다.
- 방식에 따라 다르지만, 레이어를 사용중이니 다음과 같이 로드했음
  1. 레이어 불러오기
  2. 스트리밍 소스가 있는 액터를 이동할 위치로 먼저 보냄
  3. 해당 액터가 스트리밍이 끝났는지 틱마다 체크함
  4. 끝났다면 플레이어를 해당 장소로 옮기고 위 액터는 비활성화 함.


### 스트리밍된 액터에 붙은 AI컨트롤러
 - 기존에는 AI컨트롤러의 시작지점에 빙의된 폰에다 무엇인가 했는데, 동작하지 않는다
 - 반대로 폰에서 AI컨트롤러를 통해서 무엇인가 하면 정상동작 하더라

### 세이브 파일과 월드파티션
플레이어 사망시 해당 위치에 모든 재화를 떨구게 되어 있었는데, 

플레이어 스타트 지점과 멀리 떨어져서 죽은 경우, 스폰은 가능하나 즉시 사라지는 문제가 있었다.

엔드플레이를 찍어보니 레벨 트렌지션으로 없어지는 것이었다.

근데 웃긴건 딜레이를 주고 스폰하면 아무 문제 없더라는 것이지

뭔가 로딩관련해서 일어나는 문제인거 같은데. 뭔지는 정확히 모르겠다. 아무튼 딜레이 주고 행동하는것으로 해결했다.

24.03.31 추가
정확히 말하면, 스폰 정상이지만 이동시키는 순간 트렌지션으로 사라짐.
가만히 보면 인벤토리 내부 아이템도 로드와 동시에 스폰되는데, 얘네는 이동시키지 않아서 멀쩡한건가?
