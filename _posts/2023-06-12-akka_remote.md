---
layout: post
categories: blog
title: Akka.NET/AkkaRemote
date: 2023-06-12
tags: [Akka.NET,C#]
toc:  true
---


## 개요
원격으로 다른 액터를 불러다 대화하자.
리모트 기능을 이용하면 아주 손쉽게 서버-클라이언트 구조가 나옴.(뉴비라 정확히 모르겠음 ㅎㅎ)
편의상 서버 클라이언트라고 칭하지만, 
실제로는 서버 클라이언트를 구분하지 않는다고 함.
누구나 서버이자 클라이언트가 된다는 소리.(P2P)

클라이언트가 될 프로그램의 기능을 제한하고 싶다면,
[SlimSocket](https://github.com/SaladLab/Akka.Interfaced/blob/master/docs/SlimClient.md)이라는것이 있다고 한다.

-------------------------

## 리모트와 액터 경로
ActorSelection객체로 특정 경로에 있는 액터를 가져올 수 있었음.
해당기능을 이용하면 네트워크를 통해 다른 머신에 있는 액터를 불러올 수 있는 듯 함.

----------------------------

## 리모트 액터 기능을 사용하기 위한 사전설정

다음 패키지를 추가로 설치해야 함.
1. Akka.Cluster
2. Akka.Remote
3. Hocon.Configuration
4. Akka.Streams(확실하지 않음)

설치 후 추가로 설정파일에 작성 필요.
내 프로젝트의 Config를 열고, 다음과 같이 작성.
```
<?xml version = "1.0" encoding = "utf-8"?>
<configuration >
	<configSections >
		<section name = "akka"
				 type = "Akka.Configuration.Hocon.AkkaConfigurationSection, Akka" />
	</configSections >

<akka >
		<hocon >
			<![CDATA[
		akka {
				actor {
					provider = "Akka.Remote.RemoteActorRefProvider, Akka.Remote"
				}

				remote {
					helios.tcp {
						port = 7000 #bound to a specific port
						hostname = 127.0.0.1
					}

				}
			}
      ]]>
		</hocon >
</akka >
</configuration >
```

**서버와 클라이언트 모두 있어야 하며, 로컬 테스트의 경우 반드시 포트 번호가 달라야 함.**

* 추가
굳이 config를 건들지 않고 런타임에 설정하는 방법도 있나봄   

```C#
var config = ConfigurationFactory.ParseString(@" akka.remote.dot-netty.tcp 
{ transport-class = ""Akka.Remote.Transport.DotNetty.DotNettyTransport, 
Akka.Remote"" transport-protocol = tcp port = 8091 hostname = ""127.0.0.1"" }"
);

var system = ActorSystem.Create("MyActorSystem", config);
```

위 코드를 보면, 런타임중에 설정을 만들어서 던져주는 것을 볼 수 있음.


---------------------

## 액터의 주소
![ex_screenshot](/assets/images/akka/remote-address-annotation.png)   
다음 구조를 이용하면 원격에서 특정 액터로 접근이 가능하다.


액터의 주소구조를 이용해서 원격으로 다른 프로그램에 있는 액터에게 메시지를 전달해본다.
* 서버포트는 7000, 클라이언트 포트는 7001로 설정되어 있습니다.

----------------------------

### Client

#### Program.cs
```C#
using Akka.Actor;
using Client;

Console.WriteLine("클라이언트 실행");

ActorSystem actorSystem = ActorSystem.Create("ClientActorSystem");

//ActorSelection을 이용해서, 다른 프로그램에 존재하는 TCPServer액터를 불러오고 있다.
var server = actorSystem.ActorSelection("akka.tcp://ServerActorSystem@127.0.0.1:7000/user/TCPServer");

var Client = actorSystem.ActorOf(Props.Create(() => new TCPClient(server)), "TCPClient");
Client.Tell("Send To Server HI");

actorSystem.WhenTerminated.Wait();
```

#### TCPClient.cs
```C#
public class TCPClient : UntypedActor
    {
        //로그를 위한 변수입니다.
        protected ILoggingAdapter Log = Context.GetLogger();
        //시간 측정을 위한 클래스 변수
        Stopwatch stopwatch = new Stopwatch();
        //다른 프로그램에서 받아온 서버정보입니다.
        ActorSelection Server = null;

        //이 액터가 독립적으로 동작하는가 확인하기 위한 자식 액터입니다.
        IActorRef PlayActor = Context.ActorOf<MyPlayClass>("myplaying");

        public TCPClient(ActorSelection server)
        {
            Server = server;
        }

        protected override void OnReceive(object message)
        {

            string msg = message as string;

            //정지명령이 들어왔을 때,
            if (msg.Equals("end"))
            {
                stopwatch.Stop();
                Console.WriteLine("종료");
                Console.WriteLine(stopwatch.ElapsedMilliseconds);

                /**자식 액터에서 무한루프를 돌아서 그런지 몰라도, 액터를 중지 혹은 강제제거하는 코드가 안 먹힘.*/
                // PlayActor.Tell(PoisonPill.Instance, Self);
                // Context.Stop(PlayActor);
               // PlayActor.Tell(Akka.Actor.Kill.Instance, Self);
            }
            //시작명령이 들어왔을 때,
            else if (msg.Equals("start"))
            {
                Console.WriteLine("시작");
                stopwatch.Start();
                PlayActor.Tell(msg);
            }
            //접속메시지 처리
            else if (msg.Equals("Send To Server HI"))
            {
                Server.Tell("Client : HI");
            }
            else
            {
                //  Log.Info(msg);
            }
        }


    }

    public class MyPlayClass : UntypedActor
    {
      
        protected override void OnReceive(object message)
        {
            string msg = message as string;

            if (msg.Equals("start"))
            {
                Do();
            }
        }

        private void Do()
        {
            while (true)
            {
                Task.Delay(300).Wait();
                Console.WriteLine("그러거나 말거나 나는 내 할일을 합니다.");
            }
        }

        ~MyPlayClass()
        {
            Console.WriteLine("이제 그만");
        }
    }
```

--------------------------

### Server

#### Program.cs
```C#
using Akka.Actor;
using Server;

Console.WriteLine("서버 실행");

ActorSystem actorSystem = ActorSystem.Create("ServerActorSystem");

var TCPActor = actorSystem.ActorOf<TCPServer>("TCPServer");

actorSystem.WhenTerminated.Wait();

```

#### TCPServer.cs
```C#
    public  class TCPServer : UntypedActor
    {
        //로그를 위한 변수입니다.
        protected ILoggingAdapter Log = Context.GetLogger();

        protected override void OnReceive(object message)
        {
            /**클라이언트에게서 무언가 메시지를 받으면, 20만개의 데이터를 전송합니다.*/
            Log.Info(message as string);

            //전송 시작을 알립니다.
            Sender.Tell("start");
            //단순 정수를 보냈을 때, 1.307초 걸림. 문자열 조립해서 보내면 1.6초
            for (var i = 0; i < 200000; i++)
            {
                string randMsg = "";
                //렌덤한 길이가 10인 문자열을 조립합니다.
                for(int j = 0; j < 10; j++)
                {
                    Random r = new Random();
                    randMsg +=(char) r.Next('A', 'z' + 1);
                }

                //클라이언트에게 조립한 문자열을 보냅니다.
                Sender.Tell(randMsg);
            }
            
            //끝났다고 알립니다.
            Sender.Tell("end");
        }
    }
```

단순 숫자를 20만개 보냈을 때 -> 1.3초
길이가 10인 문자열을 랜덤으로 조립해서 20만개 보냈을 때 -> 1.5~1.6초
미리 정의된 길이가 100인 문자열을 20만개 보냈을 때 -> 1.5~1.6초

이게 정상인지, 빠른것인지는 아직 몰루

![ex_screenshot](/assets/images/akka/6.png)

결과를 보면, TCPClient액터와 관계없이 MyPlayClass액터가 따로 노는 모습도 확인 가능하다.
