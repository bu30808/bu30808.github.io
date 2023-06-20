---
layout: post
categories: blog
title: UE5) 픽셀스트리밍 구성
date: 2023-06-20
tags: [UE5,UnrealEngine,PixelStreaming]
toc:  true
---

**아이피를 가려야 하는것이 맞지만, 사무실이 이전했으므로 저 아이피로 접속해봐야 아무것도 없다.**

## 구성
1. 클라이언트에게 화면을 스트리밍 해 주는 **시그널링 서버**
2. 시그널링 서버에 클라이언트를 연결(분배)해 주는 **매치메이커 서버**

반드시 TURN서버를 같이 사용해야 합니다. 

**공개 TRUN서버를 이용하면 매우 불안정하기 때문에, TURN 서버를 실행시켜서 붙여줘야 합니다.**


## 단계
1. 매치메이커 서버를 실행시킴
2. 각 포트정보와 함깨 설정된 시그널링 서버를 실행하여 매치메이커와 연결
- cmd로 시그널링 서버를 실행
- 파라미터로 전달해야 할 것
- HttpPort
- StreamerPort
- SFUPort
	-  90번 포트는 매치메이커가 사용중입니다.
	-  그 외 나머지 자유롭게 설정해주면 됩니다.
4. 게임을 특정 시그널링 서버와 연결함
- 실행 파라미터로 다음을 전달함
  - -PixelStreamingIP=localhost -PixelStreamingPort=8890
- 추가로 다음 파라미터를 전달해도 좋음
  - -AudioMixer(이 옵션이 없으면 사운드 출력이 없습니다)
  - -RenderOffScreen(드로우 자원을 아낍니다)
5. 웹으로 매치메이커에 접속하면 매치메이커가 비어있는 시그널링서버로 사용자를 할당함.









## 테스트
1. 매치메이커를 실행<br>
![ex_screenshot](/assets/images/unreal/pixelStreaming/image4.png)
<br><br><br>
2. 시그널링 실행<br>
![ex_screenshot](/assets/images/unreal/pixelStreaming/image7.png)
파라미터를 같이 전달해서 실행하고,
매치메이커에 정상적으로 접속되면 가장 아래와 같은 메시지가 출력됨.
<br><br><br>
3. 스트리밍할 게임 실행<br>
![ex_screenshot](/assets/images/unreal/pixelStreaming/image3.png)
<br><br><br>
파라미터로 시그널링서버의 포트번호를 전달하면 됨.
![ex_screenshot](/assets/images/unreal/pixelStreaming/image1.png)
<br><br><br>
성공적으로 실행되면, 스트리머가 연결되었다고 시그널링에 출력됨.
![ex_screenshot](/assets/images/unreal/pixelStreaming/image6.png)
<br><br><br>
추가로 매치메이커에서도 스트리머가 연결된 시그널링이 사용 가능하다 출력함.



4. 추가 스트리밍 실행<br>
여러 클라이언트가 접속해도 문제가 되지 않는지 확인하기 위해서,<br>
2~3번과정을 통해서 시그널링을 하나 더 매치메이커에 연결함.


5. 웹으로 매치메이커에 접속<br>
![ex_screenshot](/assets/images/unreal/pixelStreaming/image2.png)
 <br><br><br>
매치메이커 주소와 포트번호 (여기서는 매치메이커 포트번호가 90번임)로 접속
![ex_screenshot](/assets/images/unreal/pixelStreaming/image6.png)
<br><br><br>
클라이언트가 접속하면 매치메이커에서 알아서 다음 시그널링서버의 포트를 준비한다.
<br><br><br>
6. 결과<br>
![ex_screenshot](/assets/images/unreal/pixelStreaming/image8.png)
매치메이커로 접속해서 서로 다른 시그널링서버로 자동적으로 리다이렉트됨.








----







## 사설 TURN서버 연결하기
Start_SignallingServer.ps1을 메모장으로 열고,
```
$peerConnectionOptions = "{ \""iceServers\"": [{\""urls\"": [\""stun:" + $global:StunServer + "\""]}] }"
```
이 부분을
<br>
```
$peerConnectionOptions = "{ \""iceServers\"": [{\""urls\"": [\""stun:" + $global:StunServer + "\"",\""turn:서버아이피:포트번호\""], \""username\"":\""PixelStreamingUser\"", \""credential\"": \""AnotherTURNintheroad\""}] }"
```
turn 서버의 아이피와 포트번호를 직접 지정하도록 수정<br>

파워쉘로 Start_SignallingServer를 실행할 때, 파라미터 전달은 cmd로 전달하는것과 같음.<br>
<br><br>

### 기타

파워쉘로 실행시, "이 시스템에서 스크립트를 실행할 수 없으므로 ps1 파일을 로드할 수 없습니다."
<br>
라는 오류가 출력되는 경우 실행권한을 변경해주어야 함
<br>
1. 파워쉘을 관리자 권한으로 실행
2. 다음 명령어 입력
```
Set-ExecutionPolicy RemoteSigned
```
3. 예(Y) 입력
