# Springboot Webservice (2020.07.29 ~ )
AWS 와 Springboot를 사용하여 웹 서비스 구현

## Terminal
gradlew wrapper --gradle-version 4.10.2

## Library
* JUnit4
* Lombok
* JPA
* h2

## 개발 정리
* 테스트 코드
    1. TDD
        1. 항상 실패하는 테스트를 먼저 작성(RED)
        2. 테스트가 통과하는 프로덕션 코드를 작성(GREEN)
        3. 테스트가 통과하면 프로덕션 코드를 리팩토링(REFACTOR)
    2. 단위 테스트(Unit Test)
        1. TDD의 첫 번째 단계인 기능 단위의 테스트 코드를 작성
        2. TDD와 달리 테스트 코드를 꼭 먼저 작성해야 하는 것이 아님
        3. 리팩토링도 포함되지 않음
        4. 순수하게 테스트 코드만 작성
        
* 단위 테스트의 이점
    1. 개발단계 초기에 문제를 발견하게 도와줍니다.
    2. 개발자가 나중에 코드를 리팩토링하거나 라이브러리 업그레이드 등에서 기존 기능이 올바르게 작동하는지 확인할 수 있습니다.
    3. 기능에 대한 불확실성을 감소시킬 수 있습니다.
    4. 시스템에 대한 실제 문서를 제공합니다. 즉, 단위 테스트 자체가 문서로 사용할 수 있습니다.
    
* 어노테이션
    1.주석이라는 사전적 의미를 가지고있으며 , 자바 코드에 주석처럼 사용하여 컴파일 또는 런타임에서 해석된다.
      
* @SpringBootApplication
    1. @SpringBootApplication으로 인해 스프링 부트의 자동 설정, 스프링 Bean 읽기와 생성을 모두 자동으로 설정
    2. @SpringBootApplication이 있는 위치부터 설정을 읽어가기 때문에 이 클래스는 항상 프로젝트의 최상단에 위치해야만 한다.

* SpringApplication.run
    1. main 메소드에서 실행되는 것으로 내장 WAS(Web Application Server)를 실행
    2. 내장 WAS란 별도로 외부에 WAS를 두지 않고 애플리케이션을 실행할 때 내부에서 WAS를 실행하는 것
    3. 서버에 톰캣을 설치할 필요가 없고, 스프링 부트로 만들어진 Jar 파일로 실행
    4. 이로 인해 '언제 어디서나 같은 환경에서 스프링 부트를 배포' 할 수 있다.
    
* JUnit과 비교하여 assertj의 장점
    1. CoreMatchers와 달리 추가적으로 라이브러리가 필요하지 않다(Junit의 asserThat을 쓰게 되면 is()와 같이 CoreMatchers 라이브러리가 필요)
    2. 자동완성 지원(IDE에서는 CoreMatchers와 같은 Matcher 라이브러리의 자동완성 지원이 약함)

* JPA
    1. 자바 표준 ORM(Object Relational Mapping)
    2. 기능과 속성을 관리하는 객체지향 프로그래밍 언어와 데이터를 저장하는 방법에 초점이 맞춰진 관계형 데이터베이스 간의 패러다임 불일치를 해결
    3. JPA 는 인터페이스다. 따라서 JPA를 사용하기 위해서는 구현체(Hibernate,EclipseLink 등)가 필요
    
* Spring Data JPA
    1. 구현체들을 좀 더 쉽게 사용하고자 추상화시킨 모듈
    2. 구현체를 쉽게 교체할 수 있다.
    3. 저장소를 쉽게 교체할 수 있다. (MongoDB로 교체가 필요하다면 Spring Data JPA 에서 Spring Data MongoDB로 의존성만 교체하면 됨)
    4. Spring Data의 하위 프로젝트들은 기본적인 CRUD의 인터페이스가 같기 때문에 가능(Spring Data JPA, Spring Data Redis, Spring Data MongoDB 등 save(),findAll,findOne() 등을 인터페이스로 가지고 있음)
    
* @Entity
    1. 테이블과 링크될 클래스임을 나타낸다.
    2. 기본값으로 클래스의 카멜케이스 이름을 언더스코어 네이밍(_)으로 테이블 이름을 매칭한다. (ex. SalesManger.java -> sales_manager table)
    3. 가급적이면 Entity의 PK는 Long타입의 Auto_increment가 좋다(MySQL 기준 bigint 타입이 됨)
    4. 주민등록번호 또는 여러키를 조합한 복합키를 PK로 잡을 경우 난감한 상황이 발생할 수 있다.
    5. 무분별한 Setter 메소드 생성 금지 (해당 클래스의 인스턴스 값들이 언제 변하는 지 구분하기 어려워 차후 기능 변경 시 복잡해질 수 있다)
    
* @GeneragtedValue
    1. PK의 생성 규칙을 나타낸다 (PK는 @Id)
    2. 스프링부트 2.0에서는 GenerationType.IDENTITY 옵션을 추가해야만 auto_increment가 적용된다.
    
* @Column
    1. 테이블의 칼럼을 나타내며 굳이 선언하지 않더라도 해당 클래스의 필드는 모두 칼럼이 된다.
    2. 사용하는 이유는 기본값 외에 추가로 변경이 필요한 옵션이 있으면 사용한다.
    3. 문자열의 경우 VARCHAR(255) 기본값인데 사이즈를 500으로 늘리고 싶거나 타입을 TEXT로 변경하고 싶을때 사용된다.

* @NoArgsConstructor
    1. 기본 생성자 자동 추가
    2. pulbic ~~(){} 와 같은 효과
    
* @After
    1. JUnit에서 단위 테스트가 끝날 때마다 수행되는 메소드를 지정
    2. 보통은 배포 전 전체 테스트를 수행할 때 테스트간 데이터 침범을 막기 위해 사용한다.
    3. 여러 테스트가 동시에 수행되면 테스트용 데이터베이스인 H2에 데이터가 그대로 남아 있어 다음 테스트 실행 시 테스트가 실패할 수 있습니다.
    
* spring.jpa.show_sql = true
    1. src/main/resources 디렉토리 아래에 application.properties 파일을 생성한 후 위의 옵션을 추가하면 테스트 실행 시 콘솔에서 쿼리 로그를 확인 할 수 있다.
    2. spring.jpa.properties.hibernate.dialect = org.hibernate.dialect.MySQL5Dialect 를 application.properties 에 추가하면 출력되는 쿼리 로그가 MySQL 버전으로 변경된다.
    
* API를 만들기 위한 클래스
    1. Request 데이터를 받을 Dto
    2. API 요청을 받을 Controller
    3. 트랜잭션, 도메인 기능 간의 순서를 보장하는 Service
    
## 오류 및 해결
error: variable name not initialized in the default constructor private final String name (gradle 버전 문제, 5 -> 4로 다운 그레이드)
