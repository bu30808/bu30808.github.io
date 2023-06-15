---
layout: post
categories: blog
title: UE5) 머터리얼 관련 이것저것
date: 2023-06-15
tags: [UE5,UnrealEngine,material]
toc:  true
---

## 다이나믹 머터리얼 인스턴스가 적용되지 않는 경우

다이나믹 머터리얼 인스턴스를 만들어 할당했을 때,   

정상적으로 적용되었지만 그리드(머터리얼없음) 형태로 출력되는 경우

**나나이트가 사용중인 Mesh인 경우 발생**

해당 Mesh의 나나이트를 꺼주면 된다.

<br><br><br><br><br>

## 머터리얼 인스턴스의 bool 파라미터를 수정하는 법

게임을 패키징하면, 쉐이더가 전부 컴파일된 상태로 들어가기 때문에    

런타임도중 머터리얼 내부의 bool(기능을 끄거나 켬)을 변경할 수 없다고 한다.   

대안으로 해당 기능에 곱해지는 스칼라 파라미터를 주고 0을 곱하거나   

전부 활성화 해 놓은 상태에서 눈속임으로 꺼진것 처럼 보이도록 해야 한다.

<br><br><br><br><br>

# 머터리얼 인스턴스의 파라미터 값을 읽어오는 법

머터리얼 인스턴스의 파라미터(텍스쳐, 벡터, 스칼라) 정보를 읽어 올 필요가 있을 때,  
<script src="https://gist.github.com/bu30808/5296028dceca7240518ddfe566554726.js"></script>

GetAll(파라미터종류)ParameterInfo 함수를 사용하면,   

해당 파라미터 정보를 싹 긁어오는것이 가능.   

특정 파라미터 이름이 필요한 경우 FMaterialParameterInfo구조체의 Name을 읽으면 된다.
<script src="https://gist.github.com/bu30808/08ff07597ba5e9da86200a0a6b37fc4d.js"></script>

이후 Get(파라미터종류)ParameterValue 함수로 파라미터 값을 읽어올 수 있다.
