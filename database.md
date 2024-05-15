# database

- [Statement / Preparedstatement](#statement--preparedstatement)
- [Connection Pool](#connection-pool)
- [ACID](#acid)
- [Tomcat Thread Pool](#tomcat-thread-pool)
- [btree](#btree)
- [insert update delete 부하](#insert-update-delete-부하)
- [auto increment](#auto-increment)
- [saveAll](#saveall)
 
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

[위로](#database)

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

[위로](#database)

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

<br>

[위로](#database)

<br>

## Tomcat Thread Pool
tomcat은 request들에 대해 thread pool에서 유후상태인 thread를 꺼내 할당한다.

### Acceptor Thread
* 클라이언트와 연결을 수락하는 역할을 담당한다. (3 way hand-shake)
* 연결이 수립되면 대기큐에 저장한다.
* Acceptor Thread는 상대적으로 높은 우선순위를 가지며, 일반적으로 각 포트마다 하나씩 구성된다.

### Worker Thread
* 대기큐에서 작업을 꺼내 요청처리 작업을 하고 응답을 반환한다.
* max 설정으로 지정되는 수가 Worker Thread 갯수이다.

### Thread Pool이 모두 Bloking 상태에 빠진다면?
* 신규요청은 대기큐로 들어가게 된다.
* 대기큐가 꽉차면 추가요청은 거부된다. (503 에러)
* 예방하는 방법으론 외부 api 호출을 비동기로 처리, 타임아웃 설정 등이 있다.

<br>

[위로](#database)

<br>

## btree
인덱싱에 사용되는 자료구조

### b-tree
![스크린샷 2024-05-15 오전 6 20 43](https://github.com/NNIIE/Tech-Interview/assets/58434352/a878e613-83a2-492a-804d-5695d411d31e)
* 이진트리에서 파생된 트리 구조로 자식노드가 최대2개인 이진트리와 다르게 2개 이상의 자식노드를 가질 수 있다.
* 노드 내 데이터가 1개 이상일 수 있다.
* 모든 노드에 key, value가 저장된다.
* 범위검색에 취약하다
  * 루트부터 하향식으로 데이터를 찾아야 한다.

### b+tree
![스크린샷 2024-05-15 오전 6 20 52](https://github.com/NNIIE/Tech-Interview/assets/58434352/a30dbe2a-ed6f-4008-800b-46c8dd45d091)
* MySQL 엔진인 InnoDB에서 사용하는 트리 구조
* internal 노드에는 key만 저장되며, 검색경로를 결정한다.
  * 리프노드에 key, value가 저장된다.
  * internal 노드는 key만 담아두기 때문에 b-tree보다 트리의 높이가 더 낮아진다.
* 리프노드는 링크드리스트로 연결되어 있고, 옮겨가며 조회할 수 있기때문에 범위검색에 유리하다.

<br>

[위로](#database)

<br>

## insert update delete 부하
* 인덱스의 재구성
  * insert
    * 새로운 키를 삽일할 때, 해당키가 들어갈 적절한 리프노드를 찾음
    * 리프노드가 가득찰 경우, 새로운 노드를 생성하고 부모노드에 포인터를 추가함
  * delete
    * 제거된 후 노드가 너무 적은 키를 가지면, 인접한 노드와의 병합이 필요함
  * update
    * 보통 키에 연관된 데이터만 수정하는 경우가 많음
    * 업데이트가 키값을 포함하면 insert, delete와 비슷한 작업을 필요로 함
* lock
  * Exclusive lock: 쓰기작업을 수행할때 발생하고, 읽기작업도 불가능함
* acid의 지속성에 의한 로그기록

<br>

[위로](#database)

<br>

## auto increment
auto increment를 동시에 많이 처리하려면?
* InnoDB엔진 - 로우락 활용
* insert bulk 처리
* 여러 샤드로 분할하여 각 샤드가 독립적으로 auto increment를 생성하도록 함

<br>

[위로](#database)

<br>

## saveAll
* 내부적으로 여러 엔티티에 대해 save() 작업을 반복적으로 수행
* SimpleJpaRepository클래스에서 주어진 엔티티를 순회하며 각각의 엔티티를 저장
* @Transactional 어노테이션이 적용되어 모든작업이 하나의 트랜재션으로 처리됨

<br>

[위로](#database)

<br>
