---
layout: post
current: post
cover: 'assets/images/waves.jpg'
navigation: True
title: 제목을 입력합니다
date: 2021-03-12 10:18:00
tags: fiction
class: post-template
subclass: 'post'
author: zangzangs
---

# DBCP(Database Connection Pool)

데이터베이스 애플리케이션을 효율적으로 연결하는 커넥션 풀 라이브러리는 웹 애플리케이션에서는 필수 요소입니다. 웹 애플리케이션 서버로 상용 제품을 사용한다면 보통 제조사에서 제공하는 커넥션 풀 구현체를 사용합니다. 그 외에 오픈소스 라이브러리로 Apache의 Commons DBCP와 Tomcat-JDBC, BoneCP, HikariCP 등이 있습니다.

커넥션 풀 라이브러리를 잘 사용하면 데이터베이스와 애플리케이션의 일부분에서 발생하는 문제가 전체로 전파되지 않게 할 수 있고, 일시적인 문제가 긴 시간 이어지지 않게 할 수 있습니다. 반대로 값을 적절하지 못하게 설정해서 커넥션 풀이 애플리케이션에서 병목 지점이 되는 경우도 있습니다. 웹 애플리케이션의 요청은 대부분 DBMS로 연결되기 떄문에 커넥션 풀 라이브러리의 설정은 전체 애플리케이션의 성능과 안정성에 영향을 미치는 <code>핵심</code> 입니다.

## 커넥션 풀(connection pool)이란

데이터베이스와 연결된 Connection을 미리 만들어서 pool 속에 저장해 두고 있다가 필요할 때 Connection을 Pool에서 쓰고 다시 Pool에 반환하는 기법을 말합니다.

Pool 속에 미리 Connection이 생성되어 있기 때문에 Connection을 생성하는데 드는 연결 시간이 소비되지 않습니다. Connection을 계속해서 재사용하기 때문에 생성되는  Connection 수가 많지 않습니다. Connection Pool을 사용하면 Connection을 생성하고 닫는 시간이 소모되지 않기 때문에 그만큼 어플리케이션의 실행 속도가

한 번에 생성될 수 있는 Connection 수를 제어하기 때문에 동시 접속자 수가 몰려도 웹 어플리케이션이 쉽게 다운되지 않습니다.

Connection Pool에서 생성되어 있는 Connection의 갯수는 한정적입니다. 동시 접속자가 많아 남아 있는 Connection이 없을 경우 해당 클라이언트는 대기 상태로 전환이 되고, Connection이 반환되면 대기하고 있는 순서대로 Connection이 제공됩니다.

<img src="/Users/FASOL/Library/Application Support/typora-user-images/image-20210312112053976.png" alt="image-20210312112053976" style="zoom:67%;" />





## JDBC의 타임 아웃

>  **JDBC**(Java Database Connectivity)는 자바에서 데이터베이스에 접속할 수 있도록 하는 자바 표준 API이다.

DBCP는 JDBC를 이용해 생성한 Connection을 효율적으로 관리하기 위해 Connection 객체를 관리하는 것이 그 주요 목적입니다. 때문에 우리는 JDBC 드라이버에 대해 알아야 합니다.

<img src="/Users/FASOL/Desktop/스크린샷 2021-03-12 오후 1.35.20.png" alt="스크린샷 2021-03-12 오후 1.35.20" style="zoom:50%;" />

https://d2.naver.com/helloworld/1321

내용 이해 안가네..?



### WAS와 DBMS의 통신 시 타임아웃 계층

상위 레벨의 타임아웃은 하위 레벨의 타임아웃에 의존성을 가지고 있다. 하위 레벨의 타임아웃이 정상으로 동작해야 상위 레벨의 타임아웃도 정상으로 동작한다. 예를 들어, JDBC Driver SocketTimeout이 정상으로 동작하지 않으면, 그보다 상위 레벨의 타임아웃인 StatementTimeout과 Transaction Timeout도 정상으로 동작하지 않는다.

<img src="/Users/FASOL/Desktop/스크린샷 2021-03-12 오후 2.26.16.png" alt="스크린샷 2021-03-12 오후 2.26.16" style="zoom:67%;" />



### TransactionTimeout이란?

> 프레임워크나 애플리케이션 레벨에서 유효한 타임아웃

 전체 Statement 수행 시간을 허용할 수 있는 최대 시간 이내로 제한하려 할 때 TransactionTimeout을 사용한다.

간단히 TransactionTimeout을 설명하면, <code>TransactionTimeout = StatementTimeout x N(Statement 수행 수) + α(가비지 컬렉션 및 기타)</code>

예를 들어, 수행 시간이 200ms인 Statement가 5개 이하이고 기타 부수적인 비즈니스 로직 처리 시간이나 프레임워크 동작 시간이 100ms일 경우, TrasactionTimeout시간은 1100ms((200 x 5)+100) 이상으로 설정해야 한다.



### StatementTimeout 이란?

> Statement 하나가 얼마나 오래 수행되어도 괜찮은지에 대한 한계 값이다.

JDBC API인 Statement에 타임아웃 값을 설정하며, 이 값을 바탕으로 JDBC 드라이버가 StatementTimeout을 처리한다. JDBC API인 <code>java.sql.Statement.setQueryTimeout(int timeout)</code> 메서드로 설정한다.

요즘 개발 환경에서는 개발자가 직접 StatementTimeout을 Java 코드로 설정하는 경우는 드물며, 프레임워크를 이용하여 해결하는 경우가 대부분이다.



### JDBC 드라이버의 SocketTimeout 이란?

> DBMS가 비정상으로 종료되었거나 네트워크 장애(기기 장애 등)가 발생했을 때 필요한 값이다. 

TCP/IP의 구조상 소켓에는 네트워크의 장애를 감지할 수 있는 방법이 없다. 그렇기 때문에 애플리케이션은 DBMS와의 연결 끊김을 알 수 없다. 이럴 때 SocketTimeout이 설정되어 있지 않다면 애플리케이션은 DBMS로부터의 결과를 무한정 기다릴 수도 있다(이러한 Connection을 Dead Connection이라고 부르기도 한다).

이러한 상태를 방지하기 위해 소켓에 타임아웃을 설정해야 한다. SocketTimeout은 JDBC 드라이버에서 설정할 수 있다. SocketTimeout을 설정하면 네트워크 장애 발생 시 무한 대기 상황을 방지하여 장애 시간을 단축할 수 있다.

단, SocketTimeout 값을 Statement의 수행 시간 제한을 위해 사용하는 것은 바람직하지 않다. 그러므로 SocketTimeout 값은 StatementTimeout 값보다는 크게 설정해야 한다. SocketTimeout값이 StatementTimeout보다 작으면, SocketTimeout이 먼저 동작하므로 StatementTimeout 값은 의미가 없게 되어 동작하지 않는다.



## OS 레벨 SocketTimeout 설정

## ??? 다시 읽어보고 정리,,

SocketTimeout이나 ConnectTimeout을 설정하지 않으면 네트워크 장애가 발생해도 애플리케이션이 대부분 이를 감지할 수 없다. 따라서 연결이 되거나 데이터를 읽을 수 있을 때까지 애플리케이션이 무한정 기다리게 된다. 그러나 서비스에서 발생한 실재 장애 상황에서는 30분 후에 애플리케이션(WAS)이 재연결을 시도하여 문제가 해결되는 경우가 많다. OS에서도 SocketTimeout 시간을 설정할 수 있기 때문이다.

이 기사의 처음에 예로 든 리눅스 서버에서는 SocketTimeout을 30분으로 설정해 두고 있었다. 해당 설정 값을 통해 OS 레벨에서 네트워크 연결 끊김을 확인하는 것이다. 문제가 발생한 리눅스 서버의 KeepAlive 체크 수행 주기가 30분이므로 SocketTimeout 설정을 0으로 해도 네트워크 장애로 인한 DBMS 연결 장애 지속 시간이 30분을 넘지 않는 것이다. Linux 서버에서 KeepAlive 체크 수행 주기는 tcp_keepalive_time로 조정할 수 있다.

네트워크 장애로 인해 애플리케이션이 대기 상태로 빠지는 경우는 대부분 애플리케이션이 Socket.read() 메서드를 호출하고 있을 때이다. 그러나 네트워크 구성이나 장애 유형에 따라 매우 드물게 Socket.write() 메서드를 실행하는 도중 대기 상태에 빠지는 경우가 있다.

애플리케이션에서 Socket.write() 메서드를 호출하면 OS 커널 버퍼에 데이터를 기록한 후 바로 제어권을 애플리케이션에 반환한다. 즉 커널 버퍼에 값을 제대로만 기록하면 Socket.write() 메서드 실행은 언제나 성공한다. 그러나 특수한 네트워크 장애로 OS 커널 버퍼가 가득차면 Socket.write() 메서드도 대기 상황에 빠질 수 있다. 이 경우 OS는 일정 시간 동안 패킷 재전송을 시도하다고 한계 값에 도달하면 에러를 발생시킨다. 이 기사에서 예로 든 서버에서는 해당 값이 대략 15분으로 설정되어 있었다. 이 값은 Linux 서버의 tcp_retries2로 조절할 수 있다.

### TPS(transaction per seconds)

> 초당 트랜잭션 개수

실제 계산하는 방식은 일정 기간 동안 실행된 트랜잭션의 개수를 구하고 다시 1초 구간에 대한 값으로 변경합니다. 

예를들어, 커넥션 풀에 5개의 커넥션을 가진 시스템에서 요청하나의 응답 시간이 500ms 이라면 5개의 요청을 500ms에 처리 할수 있다. 1초로 계산하면 1000ms 에 10개의 요청을 처리 할 수 있으므로 성능 지수는 10TPS라고 볼 수 있다.

### 적절한 maxWait 값은?

동시에 6개의 요청이 들어왔을 때 여분의 커넥션이 없어 1개의 요청이 대기상태에 들어가게 된다.  maxWait 값을 10초(1000ms)라고 지정했다고 가정을 해보자. 앞선 요청의 처리시간이 길어져 요청의 스레드가 10초 동안 대기 상태에 있게 된다면 오류를 뿜어내며 응답을 하지 않을 것이다.

#### maxWait 값을 크게 설정하면 어떨까?

 값을 20초로 수정했다. 앞선 요청에대 한 응답이 완료되고 커넥션을 획득해 사용자의 요청을 열심히 처리하고 응답을 보냈다. 그러나 사용자는 이미 멈춰버린 응답에 사이트를 떠나고 말았을 거라는 점이다. 보통의 사람들은 클릭 후 2~3초 내에 반응이 없으면 다음 행동을 하는게 보통의 사람이라고 생각하면 쉽게 이해가 될 것이다. 결국은 응답을 받을 사용자가 없는 의미 없는 요청에 응답하기 위해 자원을 낭비한 셈이 된다. 사용자의 인내를 초과하는 maxWait 값은 아무런 의미가 없다.

#### maxWait 값을 작게 설정하면 어떨까? 

사용자가 몰린 과부하 시 커넥션 풀에 여분의 커넥션이 없을 때마다 오류가 반환될 것이다. 사용자는 너무 자주 오류 메시지를 볼 것이다.







## Referrence

https://kakaocommerce.tistory.com/45

https://d2.naver.com/helloworld/1321

https://d2.naver.com/helloworld/5102792