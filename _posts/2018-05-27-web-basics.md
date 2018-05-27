---
layout: post
title: HTTP, Web Server Architecture - 클라이언트와 호스트 간의 통신은 어떻게 이루어지는가?
category: Web
tags: Web-Server, Web-Container, Servlet, HTTP
---



최근에 스프링부트를 배우면서 클라이언트와 호스트 간의 통신이 어떻게 이루어지는지에 대한 지식이 부족한 걸 느꼈다. 기본부터 다지는 게 좋겠다는 생각에 두루뭉실하게 알고 있던 단어를 정의해보고 너무 추상적으로만 다가왔던 웹 상에서의 통신의 흐름을 정리해보았다.

### HTTP Network Basics

HTTP는 네트워크 상에 서로 통신할 수 있는 기기 간에 약속된 통신 규약이다.
  - HTTP request/response를 미리 약속된 방식으로 전송해야만 서로를 '이해'할 수 있다.
  - 예를 들면 header, 빈 줄, body 로 구분짓게 되어있고 request line은 `GET http://www.mysite.com HTTP/1.1` 메소드, URI, 통신규약/버전을 whitespace로 구분지어서 HTTP request의 첫 라인에 쓰도록 약속되어있다.


URI는 웹 상에 존재하는 리소스를 가르키는 리소스 식별자이다.
  - `http://www.helloworld.com:80/hello/world/index.html`
  - `http` 는 Scheme이라고 불리며 통신 방식을 구분짓는다.
  - `www.helloworld.com`는 호스트의 도메인주소
  - `80`은 포트 (웹서버가 사용하는 default는 80. 생략가능)
  - `hello/world/index.html`은 호스트에서 해당 리소스가 있는 path, 혹은 리소스의 '이름'이다. Query string이 될 수도 있다.


TCP/IP
  - 네트워크 상에서 두 기기가 통신할 때 서로를 식별하고, 데이터를 주고 받으려면 이에 대한 규칙 역시 약속되어 있어야한다. TCP/IP는 이러한 규정을 명시하는 프로토콜이다.
  - HTTP는 기본적으로 TCP/IP 위에서 동작한다.
  - 다른 기기에 보내지는 데이터는 IP에 의해 작은 페킷으로 나뉘어서 보내진다.
  - HTTP request/response를 한 기기가 다른 기기에게 보내면 HTTP에 약속된 형식으로 작성이 되고 TCP/IP에 의거해 통신에 기본적으로 필요한 헤더가 추가적으로 붙게된다. 예를 들면 상대 기기를 찾아가기 위해 필요한 ip address와 보내는 데이터의 무결성을 확인하기 위해 필요한 packet '일련번호' 같은 것이 있다.

> **HTTP(Hyper Text Transmission Protocol)은 그저 통신 규약(Protocol)임을 명심하자.**

### Web Server?

웹서버란 HTTP에 따라 클라이언트로부터 요청을 받고 데이터를 보내는(응답)하는 프로그램일 뿐이다. HTML이나 이미지 등을 HTTP 규격에 맞게 request를 작성해서 보내는 역할을 한다.

![Web Server](/assets/img/web-server.jpg)


### Servlet Container? (aka Web Container)

Web Container란 Web Server의 한 부분으로서, 서블릿의 인스턴스를 생성하여 lifecycle을 관리하며, HTTP 요청이 들어오면 servlet 객체의 service()를 호출한다.

![Java Servlet Architecture](/assets/img/servlet-lifecycle.jpg)

1. 서버로 오는 HTTP request는 Servlet Container에게 위임된다.
2. Servlet container는 servlet을 인스턴스화 하고 service()를 호출한다.
3. Servlet container는 들어오는 다수의 요청들을 여러 thread를 생성해서 handle한다.
  - 각 쓰레드는 하나의 servlet 인스턴스의 service() 메소드를 호출한다.


### Servlet?

Java Servlet은 JavaEE javax.servlet 패키지 안에 정의되어있는 인터페이스이다.
 

이 인터페이스를 구현하는 클래스의 인스턴스를 servlet이라고 부른다. Servlet 구현체는 세 가지의 메소드를 구현해야 한다:

1. init()
  - servlet이 만들어지면 (ie. 인스턴스화되면), init() 메소드가 호출된다.
  - life cycle 동안 사용될 데이터를 로딩한다.
2. service()
  - 요청이 들어오면 처리하는 역할을 한다.
  - GET, POST, PUT, DELETE 등의 HTTP 메소드 타입이 무엇인지 확인하고 그에 맞게 정의된 (doGet/doPost/doPut/doDelete 등의) 메소드를 호출한다.
  - service() 메소드는 servlet container에 의해 호출된다.
3. destroy()
  - destroy() 메소드가 호출되기 전까지가 서블릿의 life cycle이다.
  - DB connection을 닫고 쓰레드를 종료하는 등의 정리를 구현할 수 있다.

생성된 servlet은 들어오는 HTTP 요청을 받아서 어떻게 할지를 *동적으로* 결정할 수 있고 CGI와는 달리 요청이 들어올 때마다 새로운 프로세스를 생성하지 않기 때문에 서버의 효율을 높였다. 또한 자바 클래스이기 때문에 모든 자바 클래스 라이브러리를 사용할 수 있다.

> **요약하자면, servlet의 역할은 요청을 받아서 해석하고, 해석된 데이터에 따라 적당한 리소스를 동적으로 선택해서 보내는 것이다.**

Servlet을 implement하는 클래스를 직접 구현할 수도 있지만, 보통 Apache Tomcat과 같은 웹서버가 제공하는 구현체를 쓴다.


## 정리 - HTTP 요청이 들어오면 Web Server는 어떻게 요청을 처리하는가?

1. 웹서버가 HTTP 요청을 받는다.
2. 웹서버는 요청을 Servlet Container에게 위임한다.
3. Servlet Container는 servlet 객체를 생성해서 init()을 호출한다(해당 서블릿이 처음 로딩될 때).
4. Servlet Container는 servlet 객체의 service()를 호출한다.
  - 요청의 데이터를 읽고, 데이터에 따라 동적으로 정의된 응답을 HTTP response로 작성한다.
  - servlet은 컨테이너의 address space 안에 머물며 다른 HTTP 요청을 처리할 수 있다.
5. Web Server가 HTTP response를 요청자에게 보낸다.

![Overall Architecture](/assets/img/servlet-container-life-cycle)




참고 문서들 - References:

- https://dzone.com/articles/what-servlet-container
- https://www.tutorialspoint.com/servlets/servlets-life-cycle.htm
