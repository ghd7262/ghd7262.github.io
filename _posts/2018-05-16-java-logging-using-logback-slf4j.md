---
layout: post
title: Java - General Rules for Logging using Logback/SLF4J
category: Java Study
tags: Logback SLF4J Logging
---

## Logging - Logback/SLF4J


#### Logging의 중요성
- 문제가 생겼을 때 어디서 문제가 발생하는지 알 수 있다
- `System.out.println()`은 프로그램 성능 저하를 유발할 뿐만아니라 배포하기 전 개발 단계에서 코드에 적었던 `sout`을 일일이 다 찾아서 삭제 혹은 주석 처리해야 한다.

#### Using Logback/SLF4J Logging Framework
- Log4j 보다 성능이 좋고 제공하는 기능도 많음.
- 네 개의 logging level이 있다: `TRACE < DEBUG < INFO < WARN < ERROR`
  - logging level이 낮을 수록 더 자세한 내용을 표시: INFO level이면 WARN과 ERROR의 내용을 포함.

---

다음은 [10 Tips for Proper Application Logging](https://www.javacodegeeks.com/2011/01/10-tips-proper-application-logging.html) 에서 핵심 포인트를 요약한 내용:


##### Don’t forget, logging levels are there for you. - 네 개의 logging level을 적재적소에 활용하자.
- `ERROR` - 심각한 문제 발생. 당장 확인해야 함.
- `WARN` - 프로그램은 동작하지만 주위 깊게 살펴볼 필요 있음. 이유를 파악/분석할 필요가 있음.
- `INFO` - 중요한 비지니스 로직이 수행 됨. 현재 어플리케이션이 어떤 것을 수행하는지 로그를 읽는 사람이 파악할 수 있어야 함. 예를 들어 비행기 표를 예매하는 어플리케이션인 경우, 하나의 티켓이 구매될 때 마다 “[Who] booked ticket from [Where] to [Where]” 같이 INFO logging을 함. 추가적으로 database가 업데이트 되었거나 외부 시스템 요청 등으로 어플리케이션 중요 상태가 바뀔 때 활용.
- `DEBUG` - 개발자가 어플리케이션이 문제 없이 작동하는지 확인하기 위해 찍는 로그.
- `TRACE` - 오직 개발을 위해 사용. 아주 세부적인 내용을 포함함하고 나중에 개발 단계를 지나면 언젠가 제거할 예정인 로깅에 사용.

##### Do you know what you are logging? - 무엇을 로깅 해야할지 알고 하자.
- 상황에 맞는 문구와 필요한 정보만을 선정해서 로깅.

##### Avoid side effects. - 부수효과 발생을 피하자.
- 어플리케이션 동작을 방해하는 로깅을 조심하자.
  - `NullPointerException` 등의 logger 때문에 발생하는 exception.

##### Be concise and descriptive. - 로그는 간결하고 서술적이게.
- Data와 Description을 포함해야 한다.
- Method의 input과 output을 로그로 남기자.
  - Debugger를 사용할 필요가 없다.
  - 이런 로그는 `DEBUG`나 `TRACE`가 적절하다.
  - 외부 시스템이나 API에 연결하는 경우에도 이런 로그를 남길 필요가 있다.
- 가독성이 좋고 parsing하기 좋은 로그를 남기자.
