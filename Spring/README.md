## 트랜잭션 전파 속성
진행되고 있는 트랜잭션에서 다른 트랜잭션이 호출될 때 어떻게 처리할지 정하는 것입니다.

### 전파 설정 옵션
\@Transactional의 옵션 propagation을 통해 설정할 수 있습니다.
* REQUIRED(기본값)
  * 부모 트랜잭션이 존재한다면 부모 트랜잭션으로 합류합니다.
  * 부모 트랜잭션이 없다면 새로운 트랜잭션을 생성합니다.
  * 중간에 롤백이 발생한다면 모두 하나의 트랜잭션이기 때문에 진행사항이 모두 롤백됩니다.
* REQUIRES_NEW
  * 무조건 새로운 트랜잭션을 생성합니다.
  * 각각의 트랜잭션이 롤백되더라도 서로 영향을 주지 않습니다.
* MANDATORY
  * 부모 트랜잭션에 합류합니다.
  * 만약 부모 트랜잭션이 없다면 예외를 발생시킵니다.
* NESTED
  * 부모 트랜잭션이 존재한다면 중첩 트랜잭션을 생성합니다.
  * 중첩된 트랜잭션 내부에서 롤백 발생시 해당 중첩 트랜잭션의 시작 지점까지만 롤백됩니다.
  * 중첩 트랜잭션은 부모 트랜잭션이 커밋될 때 같이 커밋됩니다.
  * 부모 트랜잭션이 존재하지 않는다면 새로운 트랜잭션을 생성합니다.
  * 중첩 트랜잭션이 끝난 후 부모 트랜잭션에서 롤백이 발생하면 모든 트랜잭션을 롤백합니다.
* NEVER
  * 트랜잭션을 생성하지 않습니다.
  * 부모 트랜잭션이 존재한다면 예외를 발생시킵니다.
  * 부모 트랜잭션이 없다면 트랜잭션을 생성하지 않고 기능을 진행합니다.
* SUPPORTS
  * 부모 트랜잭션이 있다면 합류합니다.
  * 진행중인 부모 트랜잭션이 없다면 트랜잭션을 생성하지 않습니다.
* NOT_SUPPORTED
  * 부모 트랜잭션이 있다면 보류시킵니다.
  * 진행중인 부모 트랜잭션이 없다면 트랜잭션을 생성하지 않습니다.

## 트랜잭션 격리 수준
* 트랜잭션에서 일관성이 없는 데이터를 허용하도록 하는 수준

### READ_UNCOMMITED (커밋되지 않는 읽기, level 0)
* 아직 commit되지 않은 데이터를 다른 트랜잭션이 읽는 것을 허용합니다.
* Dirty Read 발생

### READ_COMMITED (커밋된 읽기, level 1)
* 트랜잭션이 commit 되어 확정된 데이터만을 읽도록 허용
* 커밋되기 전과 후의 select 결과가 다를 수 있습니다.

### REPEATABLE_READ (반복 가능한 읽기, level 2)
* 항상 일관성 있는 데이터 읽기를 보장
* 같은 데이터를 두 번 쿼리했을 때 일관성 있는 결과를 리턴합니다.

### SERIALIZABLE (직렬화 가능, level 3)
* 가장 높은 격리 수준
* 트랜잭션이 완료될 때까지 SELECT하는 모든 데이터에 shared lock이 걸리므로 다른 사용자는 그 영역에 해당되는 데이터 수정 및 입력이 불가능

## 의존성 주입 방법
### 수정자 주입(Setter)
* 수정자 주입을 사용하면 setter 메소드를 public으로 열어두어야 합니다.
* 즉 언제 어디서든 변경이 가능하다는 뜻입니다.

### 필드 주입
* 코드가 간결하다는 장점이 있지만 외부에서 변경이 불가능해서 테스트 하기 힘들다는 치명적인 단점이 있습니다.
* DI 프레임워크가 없으면 아무것도 할 수 없습니다.

### 생성자 주입
* 생성자 호출시점에 딱 1번만 호출되는 것이 보장됩니다.
* 생성자 주입을 사용하면 의존성을 주입을 누락하는 것을 방지할 수 있습니다.
  * IDE에서 컴파일 오류로 알려줍니다.
* 생성자를 통해 의존성을 설정하고 변경할 일이 없으므로 final로 안전하게 불변을 보장할 수 있습니다.
* 결국 생성자 주입을 이용하면 원하는 구현체를 주입할 수 있으며, 순수 자바 코드로 테스트를 실행할 수 있습니다.

## Bean Scope
* 싱글톤: 기본 스코프, 스프링 컨테이너의 시작과 종료까지 유지되는 가장 넓은 범위의 스코프
* 프로토타입: 스프링 컨테이너는 프로토타입 빈의 생성과 의존관계 주입까지만 관여하고 더는 관리하지 않는 매우 짧은 범위의 스코프
* request: 웹 요청이 들어오고 나갈때 까지 유지되는 스코프
* session: 웹 세션이 생성되고 종료될 때 까지 유지되는 스코프
* application: 웹의 서블릿 컨텍스트와 같은 범위로 유지되는 스코프

## Spring에서 싱글톤이 좋은 이유
### 웹 애플리케이션에서 싱글톤이 좋은 이유
* 웹 애플리케이션의 특징 중 하나는 여러 종류의 요청을 동시에 처리해야 한다는 것입니다.
* 만약 싱글톤 객체를 쓰지 않는다면 요청마다 요청에 필요한 객체들을 새로 생성해야 할 것이고 이 객체들은 요청이 끝난 후 gc의 대상이 될 것입니다.
* 요청이 많아지면 빈번한 gc로 인해 시스템에 악영향을 끼칠 수 있습니다.

### 싱글톤 패턴 주의점
싱글톤 패턴은 여러 클라이언트가 하나의 같은 객체 인스턴스를 공유하기 때문에 싱글톤 객체가 상태를 유지하면 안됩니다.
* 특정 클라이언트가 값을 변경할 수 있는 필드가 있으면 안됩니다.
* 읽기만 가능해야 합니다.
* 클래스 변수가 아닌 지역변수, ThreadLocal 등을 사용해야 합니다.
