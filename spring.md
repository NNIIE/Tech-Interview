# spring

- [Filter / Interceptor](#filter--interceptor)
- [Bean 생성과정](#Bean-생성과정)
- [Spring shutdown](#Spring-shutdown)
- [@Mock](#mock)
- [@Transactional](#transactional)
 
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

## mock
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

<br>

[위로](#Bean)
