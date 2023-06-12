---
layout: post
categories: blog
title: 다이나믹 메시 액터
date: 2023-06-12
tags: [DynamicMeshActor,Unreal,UnrealEngine,UE5]
toc:  true
---

런타임 도중 지오메트리를 실시간으로 수정해보자.   
라인트레이를 쏴서 클릭한 지점에 구멍을 내 보도록 하자.

1.플러그인 활성화   
 + 지오메트리 스크립트 플러그인을 켭니다.   

2.DynamicStaticActor를 상속받은 블루프린트를 생성합니다.   
3.라인트레이스를 위해서 콜리전을 켭니다
 + 다이나믹메시 컴포넌트 디테일 -> 콜리전
  + 콜리전 타입 : Simple And Complex
  + 복합 콜리전 활성화 : true
 
4.컨스트럭션 스크립트를 다음과 같이 구성합니다.   
![ex_screenshot](/assets/images/unreal/dynamicMeshActor/dynamicMesh.PNG)
 
5.구멍을 생성할 함수를 작성합니다.   
![ex_screenshot](/assets/images/unreal/dynamicMeshActor/dynamicMesh_HoleFunction.PNG) 
 
6.플레이어 캐릭터에 클릭하면 트레이스를 쏴서 구멍을 생성하는 함수를 호출합니다.    
![ex_screenshot](/assets/images/unreal/dynamicMeshActor/dynamicMesh_MouseTrace.PNG) 
 
7.결과를 확인합니다.   
![ex_screenshot](/assets/images/unreal/dynamicMeshActor/dynamicMesh_Result.PNG) 




## 기능 설명
1.AppendBox
+ 박스 형태의 다이나믹 메시를 생성합니다.
+ 특정 스태틱 메시의 형태를 그대로 가져오고 싶다면, CopyMeshFromStaticMesh를 사용하세요.   

2.SetDynamicMeshCollisionfromMesh
+ 메시 형태에 맞춘 콜리전을 활성화 시킵니다.

3.AllocateComputeMesh
+ 지오메트리를 동적으로 생성하기 위해서 호출해야 하는 함수입니다.

4.InverseTransformLocation
+ 월드좌표를 로컬좌표로 변환합니다.

5.Append(원하는 형태)
+ 원하는 형태를 가진 지오메트리를 생성합니다.

6.ApplyMeshBoolean
+ 생성된 지오메트리를 적용합니다.

7.ReleaseAllComputeMeshes
+ 지오메트리 동적 생성을 끝냅니다.   

