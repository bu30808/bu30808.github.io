---
layout: post
categories: blog
title: UE5) 안드로이드 권한 설정을 코드에서
date: 2023-07-12
tags: [UE5,UnrealEngine,Android]
toc:  true
---

안드로이드 어플리케이션을 UE로 만들어보고 있다.(익숙하니까)

DB로 파이어베이스를 써보려고 하는데, 푸시알람을 일괄적으로 보내는 기능이 있더라.

근데, 테스트하려고 안드로이드에 설치하면, 알림권한이 없어서 알림이 씹히는것 같아서 알아보는 중이다.


1. 모듈 추가
   - AndroidPermission
2. 헤더 추가
   - #include "AndroidPermissionFunctionLibrary.h"
3.  코드
```
# if PLATFORM_ANDROID
	TArray<FString> permissionName = { "android.permission.POST_NOTIFICATIONS" };
	if (!UAndroidPermissionFunctionLibrary::CheckPermission(permissionName[0]))
	{
		UAndroidPermissionFunctionLibrary::AcquirePermissions(permissionName);
	}
#endif
```

