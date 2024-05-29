# java

- [Java Agent](#java-agent)
- [byte code instrument](#byte-code-instrument)

--- 

## Java Agent
자바 어플리케이션의 런타임 동작을 수정하거나 모니터링하는 도구이다. 주로 성능 모니터링, 디버깅, 프로파일링, 코드 변환 등에 사용된다.
* **Premain method**
  * jvm 시작 시 에이전트가 로드될때 호출된다.
* **Agentmain method**
  * 이미 실행 중인 jvm 에이전트를 로드할 때 호출된다.
* **Instrumentation API**
  * 자바 에이전트가 바이트코드를 조작할 수 있도록 지원하는 API
* **에이전트 사용**
  * JVM 옵션 -javaagent를 사용하여 에이전트를 로드할 수 있다.

[위로](#java)

<br>

## byte code instrument
* bci 라고도 불리며 런타임 또는 로드때 자바 바이트코드를 수정해 소스파일의 수정없이 원하는 기능을 부여하는 기법이다.
* 모니터링 툴들이 대부분 bci기능을 이용하고 있으며, 어플리케이션의 수정없이 성능측정에 필요한 요소를 삽입할 수 있다.

[위로](#java)

<br>
