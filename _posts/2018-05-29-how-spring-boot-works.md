---
layout: post
title: 스프링 구성 요소와 동작 원리
category: Framework
tags: IoC Spring
---

스프링 부트를 사용해보면서 내부 동작 원리에 대한 이해가 필요한 것 같아서 간략하게 정리해 보았다.

### Inversion of Control
IoC는 스프링의 핵심이 되는 개념이다. 제어의 역전이란 일반적인 프로그램의 흐름을 거꾸로 바꿔서 객체 생성을 관장하는 주체가 해당 객체를 사용하는 객체가 아닌 상위 '컨테이너'가 되는 것이다.

스프링이 IoC 기반 프레임워크라고 불리는 이유는 스프링(컨테이너 혹은 어플리케이션 컨텍스트)이 필요한 시점에 필요한 어플리케이션 객체를 생성하고 사용하는 것이지 어플리케이션 코드가 스프링 객체를 사용하는 것이 아니기 때문이다.

#### Spring Beans과 Spring Container (Application Context)
클라이언트로부터 들어오는 요청과 서버에서 보낼 응답에 필요한 자바 객체와 어플리케이션의 객체를 스프링에서 내부적으로 인스턴스화하고 것을 **Spring Bean** 이라고 한다.

스프링은 HTTP 요청이 들어오면 자바에서 쓸 수 있는 서블릿 객체를 생성해고, 그 요청에 대한 응답을 작성하기 위해 어플리케이션의 객체를 생성하고 메소드를 호출한다. 이러한 Spring Bean을 생성하고 객체 간의 관계를 설정하고 servlet의 lifecycle을 관리하는 주체가 **Spring Container** 이다.

#### @Configuration & @Bean annotations
스프링이 동작할 때 사용되는 bean들을 정의해주는 클래스에 @Configuration 어노테이션을 붙인다. 간단히 생각하면 스프링 설정파일과 같은 역할을 하지만 클래스의 형태로 있는 것이다. 이 클래스의 @Bean 어노테이션을 붙인 메소드는 스프링 어플리케이션 컨텍스트가 어떤 객체가 필요한 시점에 @Configuration 객체에게 요청하면 Configuration 객체는 요청된 객체를 리턴한다.
```java
@Configuration
public class MvcConfig {

    @Bean
    public BasicAuthInterceptor basicAuthInterceptor() {
        return new BasicAuthInterceptor();
    }

    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(basicAuthInterceptor());
    }
}
```
note:
- 스프링 컨테이너에 생성된 빈들은 모두 싱글톤 패턴을 사용해 생성이 된다.
- @Configuration이 붙은 클래스에 정의되어있지 않아도 스프링 어노테이션이 달려있으면 사용 시점에 어플리케이션 컨텍스트에 빈으로 등록된다.
  - eg. @Controller


### Architecture
- Dispatcher Servlet
  - 스프링에서 클라이언트의 요청을 최초로 받아서 요청에 대응하는 컨트롤러로 라우팅해주는 front controller.
  - 어플리케이션에서 생성된 view를 클라이언트에게 response로 보내준다.
- Handler Mapping
  - Dispatcher Servlet이 받은 요청에 대응하는 컨트롤러가 무엇인지 요청하면 @Controller 어노테이션이 붙은 컨트롤러 클래스를 탐색해서 찾아주는 역할을 한다.
- Controller
  - 요청에 대한 처리(비즈니스 로직 실행 및 리소스 매핑)를 담당.
- View Resolver
  - View 객체 생성 및 데이터 주입.
- View
  - 클라이언트에게 response로 보내지게 될 객체.

[!Spring Architecutre](/assets/img/springmvcstructure.jpg)
