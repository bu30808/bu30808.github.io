---
layout: post
categories: blog
title: 컨트롤 릭 IK 적용
date: 2024-01-07
tags: [UnrealEngine,Unreal,UE5,개인프로젝트]
toc:  true
---

기본 캐릭터를 뜯다보니, 컨트롤릭이라는거를 이용해서 발 IK를 적용하고 있더라,

내가 알던 방식은 애니메이션 인스턴스 내부에서 Tick마다 따로 갱신하는 것이었지만, 

아 근데 어차피 컨트롤릭 내부에서도 Tick을 도니까 그게 그거인가 뭐 아무튼

삽질의 끝에 결국에는 IK적용에 성공했다. 

![ex_screenshot](/assets/images/unreal/myProject/24.01.07/ik01.png)

![ex_screenshot](/assets/images/unreal/myProject/24.01.07/ik02.png)

나중에 안 귀찮으면 문서로 남기도록 하겠다.
