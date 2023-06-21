---
layout: post
categories: blog
title: UE5) 서드파티 적용하기(gRPC)
date: 2023-06-21
tags: [UnrealEngine,Unreal,UE5,gRPC]
toc:  true
---

**윈도우 환경에서 진행됩니다.**

[도움이 된 이슈]([https://github.com/irajsb/UE4_Assimp/](https://github.com/alkee-allm/k2proto/issues/26)https://github.com/alkee-allm/k2proto/issues/26)


UE특성상 일반적으로 서드파티를 적용하는 방식과 조금 다르다고 합니다.

이전에 실패했던 이유는 일반적인 서드파티 적용법을 사용하고 있었기 때문이라고 생각됩니다.

아래 작성한 글을 따라가다보면, grpc 설치 부분이 있는데,

일반적인경우 install grpc만 사용하면 되지만
UE의 경우 install grpc:x64-windows-static-md 를 사용해야 합니다.

[참고한 웹](https://www.lonely.coffee/blog/2020/4/20/ue4-protobuf-and-yarn-spinner)에서는 다음과 같이 설명하고 있습니다.
```
마지막 부분(-md)은 라이브러리가 멀티스레드 특정 라이브러리로 구축되기를 원한다는 것이며,
이는 타사 코드를 Unreal에 연결하기 위한 요구 사항입니다.
```


## 진행과정
- 기본 게임모듈에 적용시켜보고,
-	플러그인으로 만들어보자

------------

## gRPC 준비하기

윈도우 환경에서 쉽게 gRPC를 준비하려면, vcpkg를 사용하는것이 편하다.
[참고](https://github.com/grpc/grpc/tree/master/src/cpp)

### vcpkg 설치
1. 터미널에서 vcpkg 설치를 원하는 폴더로 이동합니다.
  - 프로그램 빌드시 항상 vcpkg폴더를 참조합니다. 앞으로 절대 폴더를 이동시키면 안 됩니다.
2. 터미널에 다음 명령으로  vcpkg를 받습니다.

```
git clone https://github.com/Microsoft/vcpkg.git
```

3. 클론이 끝난 후 vcpkg 폴더 내부에 bootstrap-vcpkg.bat을 실행합니다.
4. 다음 명령으로 VS에 통합합니다.
```
vcpkg.exe integrate install
```

5. gRPC를 설치합니다.
   - 두가지 버전의 gRPC를 설치할 것입니다.
   - 하나는 UE에 추가될 것이고, 나머지 하나는 gRPC코드 생성용입니다.
  
6. UE에 추가할 gRPC 설치
```
vcpkg.exe install grpc:x64-windows-static-md
```

7. gRPC 코드 생성을 위한 버전 설치
```
vcpkg.exe install grpc
```

<br><br><br>
모든 설치가 끝나면, vcpkg/installed 폴더 내부에 
- x64-windows
- x64-windows-static-md
라는 폴더가 생성되어 있을 것입니다

<br><br><br><br><br>
## UE에 gRPC라이브러리 추가하기
1. UE프로젝트를 생성 후 내 프로젝트 경로로 이동한다.
2. 내 프로젝트 폴더 내부에 ThirdParty라는 폴더를 생성한다.
3. 서드파티 폴더 내부에 Protobuf라는 폴더를 생성한다.
4. Protobuf 폴더 안에 vcpkg 로 설치된 내용물들을 복사해옵니다

<br>
폴더 구조는 다음과 같이 됩니다.

```
- MyProject
  └ ThirdParty
      └ Protobuf
          └ lib
              * 여기에 vcpkg\installed\x64-windows-static-md\lib 내용물을 복사해 넣습니다.
              * 단, ossl_static.pdb 파일은 빌드오류를 만드니 제거합니다.
          
          └ include
              * 여기에 vcpkg\installed\x64-windows-static-md\include 내용물을 복사해 넣습니다.
          
          └ tools 
              * 여기에 vcpkg\installed\x64-windows-static-md\tools 내용물을 복사해 넣습니다.
              * 이 폴더는 당장 사용할 일은 없을 것입니다
```

<br><br><br>

## 내 게임에 라이브러리 적용하기

위에서 추가한 라이브러리 파일들을 코드상에서 사용하기 위해서는,   

UE에 등록할 필요가 있다.   

내 프로젝트.Build.cs 파일을 열어 다음과 같이 수정한다.   

<script src="https://gist.github.com/bu30808/0da7fdd6d2543e393c01d7d67867f90d.js"></script>

<br><br><br>

### 코드설명
```
private string ThridPartyPath
{
		get { return Path.Combine(ModuleDirectory, "../../ThirdParty"); }
}
```

ModuleDirectory는 이 Build.cs파일이 있는 경로를 가져옵니다.

이 모듈이 있는 경로에서 윗윗 폴더의 ThirdParty라는 경로(작성자의 경우 C:\Users\Elfinos\Documents\Unreal Projects\RPCTest\ThirdParty)를 ThirdPartyPath라는 이름으로 합니다.

나머지 Path 변수들도 다 비슷한 의미입니다.

<br><br>

```
ShadowVariableWarningLevel = WarningLevel.Off;
bEnableUndefinedIdentifierWarnings = false;
bEnableExceptions = true;
```
외부 라이브러리를 사용하다보면 UE에서 허용하지 않는 문법이 있는 경우가 있습니다.

위 코드로 UE에서 경고를 표시하지 않도록 합니다.

<br><br>

```
PublicIncludePaths.AddRange(
		new string[]
		{
				IncludePath
			// ... add public include paths required here ...
		}
```
추가해야할 외부 코드가 있는 경로를 알려줍니다.

<br><br>

```
PublicSystemLibraryPaths.Add(LibraryPath);
```
추가해야할 외부 라이브러리 폴더를 알려줍니다.

<br><br>

```
//이 폴더안에 있는 라이브러리를 몽땅 등록합니다.
string[] Libs = Directory.GetFiles(LibraryPath);
PublicAdditionalLibraries.AddRange(Libs); 
```
특정 폴더 내부의 .lib 파일을 몽땅 가져와서 등록합니다.

<br><br>

```
PublicDefinitions.Add("GOOGLE_PROTOBUF_NO_RTTI");
PublicDefinitions.Add("GPR_FORBID_UNREACHABLE_CODE");
PublicDefinitions.Add("GRPC_ALLOW_EXCEPTIONS=0");
```
프로토버프 관련 옵션들을 추가합니다.

<br><br>

```
PublicSystemLibraries.Add("crypt32.lib");
```
**UE에서 기본적으로 제공하는 OpenSSL의 버전이 낮아 사용할 수 없습니다.**

**최신버전의 OpenSSL을 사용하기 위해 윈도우 자체의 라이브러리를 추가합니다**

~~이것땜에 개고생함~~

<br><br><br>

---------------

## 테스트 준비

### 코드파일 준비

gRPC를 테스트 해 보려면 *.grpc.pb.cc/*.grpc.pb.h가 필요하다.

필요한 헤더와 소스를 만들려면 일단 구글 proto파일이 있어야 하는데,

예시로 사용할 proto는 [여기](https://github.com/grpc/grpc/tree/master/examples/protos)서 구할 수 있다.

링크된 git에 helloworld.proro를 받자.

<br>

또한, [여기 주소](https://github.com/grpc/grpc/tree/master/examples/cpp/helloworld)에 있는 greeter_client.cc도 받도록 하자.

greeter_client.cc는 내 게임 소스코드 경로(작성자의 경우  RPCTest\Source\RPCTest)로 옮기고,

greeter_client을 열어보면 안에 main함수가 정의되어 있는데, 

그냥 빌드하면 main때문에 안 되니까 main 함수는 제거하자.

### 코드 변환

다 받았다면 vcpkg에 설치한 grpc 변환 프로그램이 필요하다.

vcpkg\installed\x64-windows\tools\grpc 요 경로 내부에 grpc_cpp_plugin.exe가 필요하다.

편의를 위해서 해당 파일을 ThirdParty\Protobuf\tools\protobuf로 복사하도록 하자.

또한 helloworld.proto로 같은 경로로 복사하자.

#### .proto로 변환
[여기](https://qiita.com/mashira/items/bede3ad509f6f79cad19)를 참고했습니다.

#### .proto를 .pb.cc/.pb.h로 변환하기
cmd를 열고, ThirdParty\Protobuf\tools\protobuf로 이동한 뒤 다음 명령을 내린다.
```
protoc.exe -I . --cpp_out=. ./helloworld.proto
```
이것으로 c++에서 사용 가능한 구글 프로토버프 소스와 해더 파일이 생성되었다.

#### .proto를 .pb.grpc.cc/.pb.grpc.h로 변환하기
위에서 변환한 것만 가지고는 gRPC를 사용할 수 없다. 한번 더 변환해주어야 한다.

다음 커맨드를 실행한다.
```
protoc.exe -I . --grpc_out=. --plugin=protoc-gen-grpc=./grpc_cpp_plugin.exe ./helloworld.proto
```

이 커맨드가 성공했다면 gRPC에 사용할수 있는 코드가 생성될 것이다.

생성된 코드를 내 프로젝트/Source/내프로젝트 경로(작성자의 경우 RPCTest\Source\RPCTest)로 옮기도록 하자.

**복사할 경우 원본이 남아있어 빌드 에러가 생길수도 있음 잘 확인할 것**

-----------

## 테스트

어디든 내가 실행해보고 싶은 코드에 작성하면 된다.

나 같은 경우에는 GameMode안에 작성하고 1번 키 누르면 캐릭터에서 GameMode 함수 호출했다.

<script src="https://gist.github.com/bu30808/61b146258f4f0ba4a6e2c21b6e567a57.js"></script>

<br><br>

실제로 실행하기 전에 먼저 서버부터 돌려야 한다.

[서버 예제 링크](https://github.com/grpc/grpc-dotnet/tree/master/examples/Greeter)

C#으로 작성된 서버다.

서버를 먼저 실행 하고,

게임을 실행한 뒤, 1번키를 누르면

![ex_screenshot](/assets/images/unreal/thirdPartyPulgIn/image1.png)

작아서 안보이면 이미지 우클릭해서 새창에 띄우면 보임.

짜쟌 성공.
