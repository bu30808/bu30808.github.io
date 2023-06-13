---
layout: post
categories: blog
title: UE5-JSON 파일 저장 및 불러오기
date: 2023-06-13
tags: [UE5,UnrealEngine]
toc:  true
---

# 저장

레벨에 있는 액터를 Json으로 저장해보도록 하자.

1. 액터에 여러 정보를 묶어 저장해야 하니까, 구조체를 하나 만들자.
<script src="https://gist.github.com/bu30808/963107e4dff9a9bcf51cbbb970769d5b.js"></script>





2. 액터가 하나만 있는것은 아니니까 1번에서 만든 구조체로 배열을 가진 구조체를 만들자.
```
USTRUCT(BlueprintType)
struct FActorsDataArray
{
	GENERATED_BODY()
public:

	UPROPERTY(EditAnywhere)
		TArray<FActorsData> ActorsDataArray;
};
```


3. 이제 위 구조체에 저장할 대상들을 가져오면 된다.
```
//0.저장할 대상들을 가져옵니다.
TArray<AActor*> OutArr;
UGameplayStatics::GetAllActorsOfClass(this,AStructureActor::StaticClass(),OutArr);
```


4. 가져온 대상들을 구조체에 저장하자.
```
//1.저장할 데이터를 배열로 만듭니다.
FActorsDataArray DataArray;
for(auto actor : OutArr)
{
		FActorsData Data;
		Data.ActorName = actor->GetName();
		Data.ClassPath = actor->GetClass()->GetPathName();
		Data.ActorTransform = actor->GetActorTransform();
		Data.MeshPath = Cast<AStructureActor>(actor)->GetMeshComponent()->GetStaticMesh()->GetPathName();
		DataArray.ActorsDataArray.Emplace(Data);
}
```


5. 구조체를 Json으로 변환하자.
```
//2.구조체를 Json으로 변환합니다.
FString JsonString;
FJsonObjectConverter::UStructToJsonObjectString(DataArray,JsonString);
```


6. 저장하자.
```
//3.경로에 저장합니다.
if(!FFileHelper::SaveStringToFile(*JsonString,*(FPaths::ProjectDir()+"Temp.json")))
{
		ErrorLog(this,TEXT("SaveFaild"));
}
```










# 불러오기

1. 저장한 Json파일을 가져오자
```
FString JsonString;
//1.저장된 json을 가져옵니다.
if (!FFileHelper::LoadFileToString(JsonString, *(FPaths::ProjectDir() + "Temp.json"))) {
		ErrorLog(this, "");
		return;
}
```


2. 가져온 Json을 구조체로 변환하면 된다.
```
//2.Json을 구조체로 변환한다.
FActorsDataArray DataArray;
FJsonObjectConverter::JsonObjectStringToUStruct(JsonString,&DataArray);
```
