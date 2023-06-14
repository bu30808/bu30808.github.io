---
layout: post
categories: blog
title: UE5) 함수 이름으로 호출하는 방법
date: 2023-06-14
tags: [UE5,UnrealEngine,UFUNCTION]
toc:  true
---

UE의 리플렉션시스템을 이용하면 함수를 이름으로 검색해서 사용 가능.   
다만, 오버헤드가 크다고 하니 자주 사용하지 말 것.   

**반드시 함수에 UFUNCTION 메크로가 붙어 있을 것.**



다음 함수가 있다고 하자.

```
UFUNCTION()
	void FindTest();
UFUNCTION()
	void FindTestWithInt(int32 val);
UFUNCTION()
	void FindTestWithString(FString val);
UFUNCTION()
	void FindTestWithMultiParam(int32 val1, FString val2);
```


### 1. 함수 검색
```
UFunction* Function =  FindFunction("함수이름");
```

### 2. 파라미터가 없는 함수의 실행(FindTest)
```
ProcessEvent(Function,nullptr);
```
### 3. 파라미터가 있는 함수의 실행(FindTestWithInt , FindTestWithString)
```
ProcessEvent(Function,&Param);
```

### 4.파라미터가 여러 개인 함수의 실행(FindTestWithMultiParam)
<script src="https://gist.github.com/bu30808/3277958f264618304baa8c26599f5d92.js"></script>

