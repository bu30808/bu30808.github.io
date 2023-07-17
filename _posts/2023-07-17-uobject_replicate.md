---
layout: post
categories: blog
title: UE5) UObject Replicate
date: 2023-07-17
tags: [UnrealEngine,Unreal,UE5,gRPC]
toc:  true
---

## UObject Replicate


기본적으로 Actor만 Replicate를 지원한다.

특수한 경우 UObject도 Replicate시킬 수 있다.

Replicate 시킬 UObject 클래스 내부에 다음 함수를 구현   


* MyObj.h
```
virtual bool IsSupportedForNetworking() const override;

virtual bool CallRemoteFunction(UFunction* Function, void* Parms, FOutParmRec* OutParms, FFrame* Stack) override;

virtual int32 GetFunctionCallspace(UFunction* Function, FFrame* Stack) override;
```

* MyObj.cpp
```c++
bool UAbilityBase::IsSupportedForNetworking() const
{
	return true;
}


bool UAbilityBase::CallRemoteFunction(UFunction* Function, void* Parms, FOutParmRec* OutParms, FFrame* Stack)
{
	AActor* Owner = Cast<AActor>(GetOuter());
	if (!Owner) return false;
	UNetDriver* NetDriver = Owner->GetNetDriver();
	if (!NetDriver) return false;

	NetDriver->ProcessRemoteFunction(Owner, Function, Parms, OutParms, Stack, this);

	return true;
}

int32 UAbilityBase::GetFunctionCallspace(UFunction* Function, FFrame* Stack)
{
	AActor* Owner = Cast<AActor>(GetOuter());
	return (Owner ? Owner->GetFunctionCallspace(Function, Stack) : FunctionCallspace::Local);
}
```

**Outer는 네트워크에 연관되어있는 액터여야 함(ex 플레이어 컨트롤러)**



이 오브젝트를 리플리케이트하여 정보를 가지고 있어야 할 클래스 내부에 다음 함수를 구현해야 한다.

```
virtual bool ReplicateSubobjects(UActorChannel* Channel, FOutBunch* Bunch, FReplicationFlags* RepFlags) override;
```

```
bool UAbilityComponent::ReplicateSubobjects(UActorChannel* Channel, FOutBunch* Bunch, FReplicationFlags* RepFlags)
{
	bool WroteSomething = Super::ReplicateSubobjects(Channel, Bunch, RepFlags);
	
	//배열인 경우 사용
	for (auto& item : MyArray)
	{
		WroteSomething |= Channel->ReplicateSubobject(item, *Bunch, *RepFlags);
	}

	//단순 변수인 경우 사용
	WroteSomething |= Channel->ReplicateSubobject(MyReplicatedObjectValue, *Bunch, *RepFlags);
	
	return WroteSomething;
}
```





## Replicate된 UObject의 사본 생성 및 Replicate

기존처럼 DuplicateObject 함수로 복사하면, 서버에서만 존재하게 된다.

복사할 때, 플레그를 설정해주어야 정상적으로 복제되어 생성된다. 

DuplicateObject 함수에는 플래그를 넘겨줄 수 있는 기능이 없다. 

```
StaticDuplicateObject(ability,GetOwner(),NAME_None,RF_Public);
```

위 함수를 사용하면 된다.





## UObject와 RPC

일반적으로 UObject내부에서 Replicate나 RPC 호출은 불가능하다.

반드시 액터를 통해서 이루어져야 한다.

다음과 같은 경우도 안된다

**액터(서버) -> 오브젝트(멀티캐스트)**

서버에 있는 액터가 특정 오브젝트 내부의 멀티캐스트 함수를 호출해도 전혀 의미가 없다.


예외적으로 위의 UObject리플리케이션과 같이 오브젝트가 리플리케이트 되도록 코드설정을 해 준 경우는 가능하다.
