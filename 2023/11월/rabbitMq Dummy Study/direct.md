이 튜토리얼에서는 RabbitMQ와 Spring AMQP를 사용하여 라우팅 기능을 구현하는 방법에 대해 설명하고 있습니다. 이전 튜토리얼에서는 팬아웃(Fanout) 교환기를 사용하여 모든 메시지를 모든 소비자에게 브로드캐스트하는 방법을 배웠습니다. 이번 튜토리얼에서는 메시지를 특정 서브셋에만 구독할 수 있도록 확장합니다. 예를 들어, "오렌지", "블랙", "그린"과 같은 특정 색상의 메시지만을 지정하여 라우팅할 수 있습니다.

Routing with Spring AMQP
Prerequisites
로컬호스트의 표준 포트(5672)에서 실행 중인 RabbitMQ가 설치되어 있어야 합니다.
다른 호스트, 포트, 또는 자격 증명을 사용하는 경우, 연결 설정을 조정해야 합니다.
Bindings
Binding은 교환기와 큐 사이의 관계입니다.

예를 들어, Tut3Config 파일에서 이와 같은 코드를 볼 수 있습니다:

java
Copy code
@Bean
public Binding binding1(FanoutExchange fanout, Queue autoDeleteQueue1) {
return BindingBuilder.bind(autoDeleteQueue1).to(fanout);
}
Direct Exchange
팬아웃 교환기는 단순한 브로드캐스팅만 가능합니다.
대신 Direct Exchange를 사용합니다. 이 교환기의 라우팅 알고리즘은 간단합니다: 메시지는 메시지의 라우팅 키와 정확히 일치하는 바인딩 키를 가진 큐로 이동합니다.
Multiple Bindings
같은 바인딩 키로 여러 큐를 바인딩하는 것이 가능합니다.
예를 들어, 교환기 X와 Q1 사이에 바인딩 키가 "블랙"인 바인딩을 추가할 수 있습니다.
Publishing Messages
팬아웃 대신 Direct Exchange로 메시지를 보냅니다.
색상을 라우팅 키로 제공합니다.
Subscribing
이전 튜토리얼과 마찬가지로 메시지를 수신하지만, 관심 있는 각 색상에 대한 새 바인딩을 만듭니다.
Putting It All Together
Tut4Config, Tut4Sender, Tut4Receiver 클래스를 만듭니다.
메시지를 보내고 받는 방법을 구현합니다.
Compile and Run
Maven을 사용하여 컴파일하고 JAR 파일에서 옵션을 실행합니다.
Full Source Code
Tut4Receiver.java, Tut4Sender.java, Tut4Config.java의 전체 소스 코드를 참조할 수 있습니다.
Next Steps
튜토리얼 5로 이동하여 패턴을 기반으로 메시지를 수신하는 방법을 배웁니다.
이 튜토리얼은 RabbitMQ와 Spring AMQP를 사용하여 메시지를 특정 서브셋에만 라우팅하는 방법을 보여줍니다. 이를 통해 더 세밀한 메시지 처리가 가능해집니다.