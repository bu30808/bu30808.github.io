---
layout: post
categories: blog
title: UE5) 에셋 로드
date: 2023-06-14
tags: [UE5,UnrealEngine,AsyncLoading]
toc:  true
---


## 동기식 런타임 로드

TSoftClassPtr는 Unreal Engine에서 Asset을 참조하는 데 사용되는 구조체 중 하나입니다.  
 
Asset을 TSoftClassPtr로 참조하면 빌드 시점에 클래스의 참조를 직접 삽입하는 대신, 

Asset의 경로를 가리키는 문자열이 저장됩니다.   

이렇게 함으로써 클래스의 메모리 로딩을 늦추어 빌드 시간을 단축할 수 있습니다.

TSoftClassPtr은 클래스 타입을 지정할 수 있으며, 해당 클래스의 유효성 검사를 수행합니다.   

클래스가 존재하지 않는 경우, TSoftClassPtr은 유효하지 않은 것으로 표시됩니다.

다만, 동기식으로 로드하는 경우에는 로드중에 멈출 수 있습니다.

TSoftClassPtr을 사용하여 Asset을 로드하려면 다음과 같은 절차를 따르면 됩니다.


⦁	 TSoftClassPtr 변수를 선언합니다.
```
TSoftClassPtr<class UClass> AssetClassPtr;
```
⦁	LoadSynchronous() 함수를 사용하여 Asset을 로드합니다.
```
UClass* AssetClass = AssetClassPtr.LoadSynchronous();
if (AssetClass != nullptr)
{
    // 로드된 Asset을 사용합니다.
}
```



### 주의!! 
만약 로드하려는 대상이 블루프린트인경우에는 조금 달라집니다.

    UClass* AssetClass = AssetClassPtr.LoadSynchronous();
    if (AssetClass != nullptr)
    {
        //로드한 클래스가 블루프린트인경우, 사용하고자 하는 클래스로 cast하면 cast에 실패합니다.
        //다음 클래스로 먼저 cast 한 후 COD를 가져와서 사용해야 합니다.
        auto MyLoadedObject = Cast<UBlueprintGeneratedClass>(AssetClass)->GetDefaultObject<T>();
    }

⦁	TSoftClassPtr은 게임실행중 Asset을 로드하는 기능도 제공합니다.   
⦁	TSoftClassPtr은 로드가 완료될 때까지 유효하지 않으며, Get() 함수를 호출하여 로드된 Asset에 액세스해야 합니다.




### 이런 방식으로도 사용할 수 있습니다.
```
// 동기식으로 Asset을 로드합니다.
AssetClassPtr.LoadAsset();
// 로드가 완료될 때까지 대기합니다.
while (!AssetClassPtr.IsPending())
{
    // 대기 중...
}
// 로드된 Asset에 액세스합니다.
UClass* AssetClass = AssetClassPtr.Get();
if (AssetClass != nullptr)
{
    // 로드된 Asset을 사용합니다.
}

```





⦁	Asset의 경로는 문자열 리터럴, FString 또는 FName을 사용하여 설정할 수 있습니다.

```
// 문자열 리터럴을 사용하여 경로를 설정합니다.
AssetClassPtr = TEXT("/Game/MyFolder/MyAssetClass");
// FString을 사용하여 경로를 설정합니다.
AssetClassPtr = FString("/Game/MyFolder/MyAssetClass");
// FName을 사용하여 경로를 설정합니다.
AssetClassPtr = FName("/Game/MyFolder/MyAssetClass");
```
마지막으로, TSoftClassPtr은 Blueprint 내에서도 사용할 수 있습니다.    
⦁	Blueprint 내에서 TSoftClassPtr은 UBlueprintSoftClassReference 클래스를 사용하여 선언합니다.    
⦁	Blueprint에서 TSoftClassPtr 변수에 대한 액세스는 TSoftClassPtr 구조체와 거의 동일합니다.   


-------------

## 비동기식 런타임 로드

FStreamableManager를 이용하면 비동기식으로 런타임중에 에셋을 로드할 수 있다.
<script src="https://gist.github.com/bu30808/8998aef6baf7babe0a833bd02aac4c07.js"></script>



