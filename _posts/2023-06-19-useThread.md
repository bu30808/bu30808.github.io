---
layout: post
categories: blog
title: UE5) Thread사용
date: 2023-06-19
tags: [UE5,UnrealEngine,Thread]
toc:  true
---

## 직접 Thread를 정의해서 사용

FRunnable 클래스를 상속받는것으로 Thread를 정의할 수 있다.

헤더
<script src="https://gist.github.com/bu30808/ce269d5e696e2e5e69e1ca77b0ae1388.js"></script>


코드
<script src="https://gist.github.com/bu30808/eb8bbca3fdd2c9336465b1d60ff9ba9b.js"></script>

사용법
<script src="https://gist.github.com/bu30808/1c6195688e77ab9855839982222cff59.js"></script>

-------------

## 간단한 비 동기 Thread 사용

AsyncTask를 사용하면 Thread를 생성해 즉시 사용 가능하다,

만약 C#과 같다면, 

이 방식으로 사용할 경우 TreadPool에 여유가 있으면서, 놀고있는 Thread가 없는 경우에만 Thread가 생성되서 할당 될 것이고,

그렇지 않다면 놀고있는 Thread가 이 작업을 처리할 것이다.

```
AsyncTask(ENamedThreads::AnyHiPriThreadNormalTask, []()
{
	//Do Something
});
```

또한 AsyncTask는 중첩 가능하다.

------

## 외부 Thread에서 GameThread로의 작업 요청

GameThread에서만 가능한 작업들을 처리할때 사용한다.

일부 코드는 GameThread 이외에서 동작하지 못 하도록 막혀있는데, 그 때 사용 가능할만한 코드이다.

ENamedThreads 열거형 값에 따라서 다른 Thread로의 요청이 가능하다.

```
AsyncTask(ENamedThreads::AnyHiPriThreadNormalTask, []()
{
	//다른 Thread에서 할 일

	AsyncTask(ENamedThreads::GameThread, []()
	{
		//GameThread에서 처리해야만 할 일.
	});
});
```
----

## 스레드 사용과 뮤텍스 락

[쓰레드](https://michaeljcole.github.io/wiki.unrealengine.com/Multi-Threading:_How_to_Create_Threads_in_UE4/#starting-the-thread)

[뮤텍스 락](https://michaeljcole.github.io/wiki.unrealengine.com/MultiThreading_and_synchronization_Guide/)

나중에 시간있으면 정리해서 다시 씀.

--------

## 스레드와 텍스처 로드
텍스처는 반드시 게임 스레드에서 로드되어야 합니다.
텍스처 복사 작업이 다른 스레드에서 발생해선 안 됩니다.
