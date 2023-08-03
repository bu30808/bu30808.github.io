---
layout: post
categories: blog
title: UE5) 플레이팹 리더보드
date: 2023-08-03
tags: [UnrealEngine,Unreal,UE5,PlayFab]
toc:  true
---

플레이팹에는 플레이어 순위를 매길 수 있는 리더보드 기능을 제공한다.

리더보드를 편하게 추가하려면 대시보드를 이용하면 되고, 코드로도 추가할 수 있다.

리더보드는 설정에 따라

매 시간, 매 일, 매 주, 매 월 단위로 업데이트 되도록 설정 할 수 있다.

## 리더보드 데이터 업로드

<script src="https://gist.github.com/bu30808/7c92f10171e8f7b0c1e007569a0f6c3d.js"></script>   

<br>

이전과 비슷하게 리퀘스트를 만들어서 던져주면 된다.

다른건 알 필요 없고, 리퀘스트에다 어떤 리더보드를 읽을지, 점수는 얼마만큼 저장할지 정해주면 된다.

성공적으로 업데이트가 되었다면, 대시보드에서 확인 가능하다.


## 리더보드 데이터 읽어오기

<script src="https://gist.github.com/bu30808/1271b71141f95d02bb274a6d1ac56cf2.js"></script>   

<br>

이번에도 리퀘스트에다가 어떤 리더보드를 어디서부터 얼마만큼 읽어올지 담아서 보내면 된다.

성공적으로 읽어온 경우, 리더보드 데이터가 담긴 배열을 받아 올 수 있다.

위 코드의 경우 델리게이트를 실행하고 있는데, 해당 델리게이트는 대충 이런식으로 정의되어 있다.

```
OnGetRankList.BindLambda([this](const TArray<PlayFab::ClientModels::FPlayerLeaderboardEntry>& RankList)
{
    //뭐든 합니다!
    for(auto data : RankList){
      //순위
      data.Position;
      //유저 닉네임
      data.DisplayName;
      //저장된 값
      data.StartValue;
    }
});
```

