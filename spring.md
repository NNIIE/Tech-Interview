# spring

- [Filter / Interceptor](#filter--interceptor)
- [Bean 생성과정](#Bean-생성과정)
- [Spring shutdown](#Spring-shutdown)
- [Mockito](#mockito)
- [@Transactional](#transactional)
- [@SpringBootApplication](#springbootapplication)
- [AOP](#aop)
 
--- 

## Filter / Interceptor
요청의 전/후처리를 할 수 있도록 하는 기술
* **Filter**
  * 요청이 Dispatcher Serlvet에 전달되기 전/후에 부가작업을 처리할 수 있다.
  * 스프링 컨테이너가 아닌 서블릿 컨테이너에 의해 관리된다.
  * `javax.servlet.Filter` 인터페이스를 구현하고 `doFilter()` 를 오버라이딩 하여 사용
  * 용도
    * 보안관련 공통작업 (요청을 스프링 컨테이너까지 전달하지 않게 차단할 수 있다)
    * 모든 요청에 대한 로깅
    * 이미지 / 데이터 압축 및 문자열 인코딩
* **Interceptor**
  * Dispatcher Serlvet이 Controller를 호출하기 전/후에 부가작업을 처리할 수 있다.
  * 서블릿 컨테이너가 아닌 스프링 컨테이너에서 동작한다.
  * 용도
    * 인증/인가 공통작업 (Controller로 넘어가기 전에 인증/인가 검사)
    * api 호출에 대한 로깅
    * HttpServletRequest, HttpServletResponse 객체를 가공하여 Controller에 전달
* **차이점**
  * 필터는 서블릿 컨테이너에서 실행되고, 인터셉터는 스프링 컨테이너에서 실행되므로 실행 시점이 다르다.
  * 필터는 디스패처 서블릿 전후를 다룰 수 있고, 인터셉터는 컨트롤러 전후를 다를 수 있다.
  * 필터는 스프링이 아닌 전역적으로 처리해야 하는 작업을 하고, 인터셉터는 스프링으로 들어온 요청에 대해 전역적으로 처리한다.

<br>

[위로](#spring)

## Bean Lifecycle
1. 스프링 컨테이너 생성
   * Application Context가 설정정보를 로딩
2. 빈 생성 및 의존성 주입
   * 빈의 인스턴스를 생성하고, DI를 통해 빈 간의 의존관계 설정
3. 초기화
   * 빈이 생성되고 의존관계가 설정되면 빈을 초기화
   * 추가 설정등을 수행한다. ex) @PostConstruct
4. 빈 사용
5. 소멸 전 콜백
   * 어플리케이션 종료과정에서 빈이 제거되기 전 필요한 자원을 반환하거나, 추가적인 정리작업을 수행
   * destroy() 메서드 또는 @PreDestroy가 붙은 메서드 호출
6. 스프링 종료
   * 빈들이 생성된 순서의 역순으로 소멸
* 생성자 주입 DI를 선호하는 이유
  * 객체를 사용할때 해당 객체가 갖는 의존성이 모두 갖추어졌다 보장할 수 있다.
 
<br>

[위로](#Bean)

## Spring shutdown
- spring shutdown될때 벌어지는 현상
  - `server: shutdown: immediate`
  - 진행중인 요청들에 대한 완료를 기다리지 않고 즉시 종료
  - 빈 소멸 & 자원 해제
- graceful shutdown
  - `server: shutdown: graceful`
  - 진행중인 요청들이 완료된 다음 종료
  - 빈 소멸 & 자원 해제
  - 옵션
    - `spring.lifecycle.timeout-per-shutdown-phase`
      - 빈들의 종료과정에 대한 시간
      - defualt: 30s
    - `server: tomcat: graceful-shutdown-timeout:`
      - 진행중인 HTTP 요청에 대한 완료 시간

<br>

[위로](#Bean)

## Mockito
* @Mock
  * 가짜 객체를 생성한다.
  * 실제 동작은 하지 않는다.
* @MockBean
  * 스프링 애플리케이션 컨텍스트에 등록된 빈을 mock 객체로 교체한다.
  * 실제 동작은 하지 않는다.
* @Spy
  * Java 객체의 인스턴스를 스파이 객체로 생성한다.
  * 스파이 객체는 실제 로직을 수행하지만, 선택적으로 특정 메서드의 행동을 모킹할 수 있다.
* @SpyBean
  * 스프링 애플리케이션 컨텍스트에 등록된 빈을 스파이 객체로 교체한다.
  * 원본 빈과 동일한 동작을 수행하면서, 선택적으로 특정 매서드의 행동을 모킹할 수 있다.
* @InjectMock
  * mock, spy 객체를 @InjectMock이 붙은 객체에 주입한다.

<br>

[위로](#Bean)

## @Transactional
스프링에서 제공하는 AOP를 활용한 선언적 트랜잭션 관리

### propagation
여러 트랜잭션이 관련된 경우, 트랜잭션 전파방식을 결정한다.
* required
  * 트랜잭션이 있는경우 참여하고 없으면 새 트랜잭션을 생성한다.
  * default 값이다.
* requires_new
  * 항상 새 트랜잭션을 생성하고, 이미 존재할 경우 일시중지 한다.
* nested
  * 트랜잭션이 존재하면 지원하고, 없으면 트랜잭션없이 메서드만 실행한다.
* mandatory
  * 반드시 트랜잭션이 존재해야 하며 없으면 예외 발생
* not_supported
  * 트랜잭션이 있어도 중단되며, 트랜잭션을 지원하지 않는다.
* never
  * 트랜잭션이 존재하면 예외가 발생한다.

### isolation
트랜잭션 격리 수준을 설정한다. 기본값은 DB의 격리수준이다.
* read_uncommitted
  * 가장 낮은 격리수준으로, 다른 트랜잭션에서 commit 되지 않은상태의 데이터까지 읽어온다.
* read_committed
  * uncommitted과 반대로 commit 된 내용만 읽어온다.
  * 트랜잭션들이 동시에 수행되고 있다면 트랜잭션들의 commit 이후 동시성 문제가 발생할 여지가 있다.
* repeatable_read
  * 하나의 트랜잭션에 하나의 스냅샷을 이용하기 때문에 동시성 문제는 발생하지 않는다.
  * 다시 데이터를 조회하는 과정에 새로 추가되거나 제거된 값을 가져올 수 있다.
* serializable
  * 가장 높은 격리수준
  * read시 dml 작업이 불가능하다.

### DBCP와 @Transactional
* @Transactional은 트랜잭션을 생성할 때, db connection을 하나 점유하게 된다.
* 트랜잭션이 제대로 종료되지 않고 예외가 발생해 연결이 풀로 반환되지 않는경우, 연결 누수가 발생한다.
* required의 경우 여러 트랜잭션이 하나의 연결풀을 사용한다.

### PlatformTransactionManager
* mybatis, jpa, redis 등에서 트랜잭션이 동일하게 동작하지 않는다.
* PlatformTransactionManager == 트랜잭션을 추상화 시킨 인터페이스.
  * 주요구현체
    * DataSourceTransactionManager: JDBC
    * JpaTransactionManager: JPA
    * HibernateTransactionManager: Hibernate


<br>

[위로](#Bean)

## @SpringBootApplication
Spring Boot 애플리케이션을 간편하게 정의해서 실행할 수 있도록 하는 어노테이션
* @Configuration
  * Spring IoC 컨테이너가 관리할 빈을 정의
* @EnableAutoConfiguration
  * Spring Boot가 애플리케이션의 클래스 경로에 있는 다양한 라이브러리를 기반으로 자동 설정을 활성화
  * ex) 클래스 경로에 spring-boot-starter-web 라이브러리가 있다면, 자동으로 DispatcherServlet 등을 설정
* @ComponentScan
  * Spring이 지정된 패키지와 그 하위 패키지에서 컴포넌트를 검색하여 빈으로 등록

<br>

[위로](#Bean)

## AOP

### JDK 동적프록시, CGLIB 프록시

### Weaving
Aspect를 대상 객체에 적용하는 과정으로 컴파일, 로드, 런타임 시점에 이루어짐
* **compile-time weaving**
  * 소스코드가 컴파일될 때 aspect를 대상 코드에 적용해서 aspect가 적용된 바이트코드 생성
  * 컴파일러에 위빙 기능을 통합하여 이 작업을 수행
* **post-compile-time weaving**
  * 이미 컴파일된 즉, 바이트코드가 생성된 후 aspect를 적용
* **load-time weaving**
  * jvm에 로드될 때 aspect를 적용
  * 자바 에이전트나 클래스 로더를 통해 클래스가 로드될 때 위빙을 수행
* **run-time weaving**
  * 런타임중 프록시 객체를 사용해 aspect를 적용
  * Spring AOP는 주로 런타임위빙을 통해 프록시 패턴으로 aspect를 적용한다.

<br>

[위로](#Bean)
