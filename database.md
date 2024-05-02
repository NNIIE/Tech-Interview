# network

- [Statement / Preparedstatement](#statement--preparedstatement)
- [Connection Pool](#connection-pool)
- [ACID](#acid)
 
--- 

## Statement / Preparedstatement
쿼리를 실행하는데 사용되는 인터페이스
### Statement
* 문자열 기반 쿼리를 실행한다.
* 캐시 사용이 불가능하다.
  * 매번 실행시 쿼리분석 - 컴파일 - 실행 단계를 거친다.
* sql 인젝션에 취약
* Connection 생성 - Statement 생성 - 실행 - ResultSet객체로 결과 반환 - 순서대로 Close
### PreparedStatement
* 매개변수 기반 쿼리를 실행한다.
* 캐시 사용이 가능하다.
  * 실행시 처음 한번만 쿼리분석 - 컴파일 - 실행 단계를 거치고 그후엔 캐싱해서 사용한다.
* sql 인젝션에 안전
* Connection 생성 - PreparedStatement 생성 - 파라미터 바인딩 - 실행 - ResultSet객체로 결과 반환 - 순서대로 Close
<br>

[위로](#network)

<br>

## Connection Pool
WAS가 실행되면 Connection 객체들을 미리 만들어두고 pool에 저장하여 재사용 하는 방식
<br>
자바에서는 DataSource 인터페이스를 사용하여 Connection Pool을 관리한다.
### 종류
* Apache - Commons DBCP
* Tomcat - JDBC
* HikariCP (스프링부트 default)
### 설정
* **maximum-pool-size** : 최대 pool size (defailt 10)
* **connection-timeout** : 커넥션의 타임아웃
* **validation-timeout** : 유효한 타임아웃
* **minimum-idle** : 연결 풀에서 HikariCP가 유지 관리하는 최소 유휴 연결 수
* **idle-timeout** : 연결을 위한 최대 유휴 시간
* **max-lifetime** : 닫힌 후 pool 에있는 connection의 최대 수명 (ms)입니다.
* **auto-commit** : auto commit 여부 (default true)

<br>

[위로](#network)

<br>

## ACID
DB에서 안정적으로 트랜잭션을 처리하기 위해 필요한 4가지 속성
### Atomicity(원자성)
- 트랜잭션 내의 연산들은 모두 성공하거나, 모두 실패해야 한다.
### Consistency (일관성)
- 트랜잭션 이전과 이후, 데이터베이스의 무결성 제약조건은 항상 만족되어야 한다.
### Isolation (고립성)
- 모든 트랜잭션은 다른 트랜잭션으로부터 독립되어야 한다.
### Durability (지속성)
- 하나의 트랜잭션이 성공적으로 수행되었다면, 해당 트랜잭션에 대한 기록이 남아야 한다.

