# spring

- [Filter / Interceptor](#filter--interceptor)
 
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

<br>
