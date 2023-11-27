## RabbitMQ와 Spring AMQP를 사용한 Publish/Subscribe 패턴
이 튜토리얼에서는 RabbitMQ와 Spring AMQP를 사용하여 Publish/Subscribe 패턴을 구현하는 방법에 대해 설명합니다. 이 패턴은 메시지를 여러 소비자(Consumers)에게 동시에 전달하는 데 사용됩니다.

기본 개념
Producer(생산자): 메시지를 보내는 애플리케이션입니다.
Queue(큐): 메시지를 저장하는 버퍼입니다.
Consumer(소비자): 메시지를 받는 애플리케이션입니다.
Exchange(교환기): Producer는 메시지를 직접 큐에 보내지 않고 Exchange에 보냅니다. Exchange는 받은 메시지를 큐에 전달합니다.
Exchange 타입
Direct, Topic, Headers, Fanout 등이 있습니다. 이 튜토리얼에서는 Fanout에 집중합니다.
Fanout Exchange: 받은 메시지를 알고 있는 모든 큐에 브로드캐스트합니다.
구성(Configuration)
Fanout Exchange 설정
java
Copy code
@Bean
public FanoutExchange fanout() {
return new FanoutExchange("tut.fanout");
}
Anonymous Queues
임시 큐를 생성합니다. 이 큐들은 고유한 이름을 가지며, 소비자가 연결을 끊으면 자동으로 삭제됩니다.
java
Copy code
@Bean
public Queue autoDeleteQueue1() {
return new AnonymousQueue();
}
Bindings
Exchange와 큐 사이의 관계를 정의합니다.
java
Copy code
@Bean
public Binding binding1(FanoutExchange fanout, Queue autoDeleteQueue1) {
return BindingBuilder.bind(autoDeleteQueue1).to(fanout);
}
Producer(생산자)
메시지를 Fanout Exchange에 보냅니다.
RabbitTemplate을 사용하여 메시지를 전송합니다.
java
Copy code
template.convertAndSend(fanout.getName(), "", message);
Consumer(소비자)
@RabbitListener 어노테이션을 사용하여 특정 큐에서 메시지를 수신합니다.
메시지를 받으면 처리합니다.
java
Copy code
@RabbitListener(queues = "#{autoDeleteQueue1.name}")
public void receive1(String in) {
// 메시지 처리
}
핵심 포인트
Publish/Subscribe 패턴: 한 메시지가 여러 소비자에게 동시에 전달됩니다.
Fanout Exchange: 모든 메시지를 알고 있는 모든 큐에 브로드캐스트합니다.
임시 큐: 소비자가 연결을 끊으면 자동으로 삭제되는 임시 큐를 사용합니다.
팬아웃 방식의 중복 처리 문제
중복 저장: 여러 소비자가 동일한 메시지를 받으면, 예를 들어 데이터베이스에 저장하는 로직이 있다면 중복 저장될 수 있습니다.
중복 처리 방지: 중복 처리를 방지하기 위해서는 메시지에 고유 식별자를 포함시키고, 소비자가 처리하기 전에 이를 확인하는 로직을 구현해야 합니다.
팬아웃 방식의 사용 사례
이벤트 알림: 시스템의 특정 이벤트를 여러 서비스나 시스템에 알려야 할 때 유용합니다.
로그 수집: 시스템에서 발생하는 로그를 여러 대의 로그 처리 시스템으로 전송하여 분산 처리할 때 사용됩니다.
실시간 데이터 피드: 실시간으로 변하는 데이터(예: 주식 시세, 뉴스 피드 등)를 여러 클라이언트에 동시에 전송해야 할 때 사용됩니다.
주의사항
팬아웃 방식은 메시지를 모든 소비자에게 브로드캐스트하기 때문에, 각 소비자가 독립적으로 메시지를 처리할 수 있어야 합니다.
중복 처리나 부하 관리 등 추가적인 고려사항이 필요합니다.