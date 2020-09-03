# Springboot Webservice (2020.07.29 ~ 2020.09.01)
스프링 부트와 AWS로 혼자 구현하는 웹 서비스(책 따라하기)

## Terminal
gradlew wrapper --gradle-version 4.10.2

## Dependency
* JUnit4
* Lombok
* JPA
* h2
* mustache
* oauth2
* jdbc
* mariadb
* security-test


## application.properties
* src/main/resources 디렉토리 아래에 생성
    1. spring.jpa.show_sql = true (테스트 실행 시 콘솔에서 쿼리 로그를 확인 할 수 있다)
    2. spring.jpa.properties.hibernate.dialect = org.hibernate.dialect.MySQL5Dialect (출력되는 쿼리 로그가 MySQL 버전으로 변경된다)
    3. spring.h2.console.enabled = true (웹 콘솔 옵션)
    4. spring.profiles.include = xxx (appication-xxx.properties 에서 적용한 값을 프로젝트에 설정한다)
    5. spring.security.oauth2.client.registration.google.scope = profile,email (기본 값은 profile,email,openid 여기서 openid가 적용 될 경우 OpenId Provider인 서비스 구글과 그렇지 않은 네이버 등 두가지로 나눠서 Oauth2Service를 만들어야 하기 때문에 하나로 만들기 위해 openid scope를 빼고 등록한다)
    6. spring.session.store-type = jdbc (세션 저장소를 jdbc로 선택)

## Spring Boot Annotation
주석이라는 사전적 의미를 가지고있으며 , 자바 코드에 주석처럼 사용하여 컴파일 또는 런타임에서 해석된다.
          
    * @SpringBootApplication
        1. @SpringBootApplication으로 인해 스프링 부트의 자동 설정, 스프링 Bean 읽기와 생성을 모두 자동으로 설정
        2. @SpringBootApplication이 있는 위치부터 설정을 읽어가기 때문에 이 클래스는 항상 프로젝트의 최상단에 위치해야만 한다.
    
    * @RequiredArgsConstructor
        1. 선언된 모든 final 필드가 포함된 생성자를 생성해 준다.
        2. final이 없는 필드는 생성자에 포함되지 않는다.
        3. 스프링에서 Bean을 주입받는 3가지 방법(@Autowired, setter, 생성자) 중 가장 권장하는 방식인 생성자로 주입 방식과 동일한 효과를 볼 수 있다.
        4. 생성자를 직접 쓰지 않고 롬복 어노테이션을 사용하는 이유는 해당 클래스의 의존성 관계가 변경될 때마다 생성자 코드를 계속해서 수정할 필요가 없기 때문이다.
          
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
        
    * @MappedSuperclass
        * JPA Entity 클래스들이 BaseTimeEntity를 상속할 경우 필드들(createdDate, modifiedDate)도 칼럼으로 인식하도록 합니다.
    
    * @EntityListeners(AuditingEntityListener.class)
        * BaseTimeEntity 클래스에 Auditing 기능을 포함시킵니다.
    
    * @CreatedDate
        * Entity가 생성되어 저장될 때 시간이 자동 저장됩니다.
        
    * @LastModifiedDate
        * 조회한 Entity의 값을 변경할 때 시간이 자동 저장됩니다.

    * @EnableJpaAuditing
        * 메인 클래스의 위에서 설정되어 JPA Auditing 어노테이션들을 모두 활성화 시킵니다.

    * @Transactional
        1. 스프링에서 지원하는 선언적 트랜잭션(데이터베이스의 상태를 변경시키는 작업 또는 한번에 수행되어야 할 작업)
        2. readOnly 읽기 전용으로 설정(트랜잭션의 범위는 유지하되, 조회 기능만 남겨두어 조회 속도가 개선되기 때문에 등록,수정,삭제 기능이 전혀 없는 서비스 메소드에서 사용하는 것이 좋다)

    * @Enumerated(EnumType.STRING)
        1. JPA로 데이터베이스로 저장할 때 Enum 값을 어떤 형태로 저장할지를 결정합니다.
        2. 기본적으로는 int로 된 숫자가 저장됩니다.
        3. 숫자로 저장되면 데이터베이스로 확인할 때 그 값이 무슨 코드를 의미하는지 알 수가 없습니다.
        4. 그래서 문자열(EnumType.STRING)로 저장될 수 있도록 선언합니다.
    
    * @EnableWebSecurity
        * 스프링 시큐리티 설정들을 활성화 시켜줍니다.
     
    * @WithMockUser(roles="USER")
        1. 인증된 모의 사용자를 만들어서 사용합니다.
        2. roles에 권한을 추가할 수 있습니다.
        3. 즉, 이 어노테이션으로 인해 ROLE_USER 권한을 가진 사용자가 API를 요청하는 것과 동일한 효과를 가지게 됩니다.
    
    
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
  

* API를 만들기 위한 클래스
    1. Request 데이터를 받을 Dto
    2. API 요청을 받을 Controller
    3. 트랜잭션, 도메인 기능 간의 순서를 보장하는 Service
    
* Spring 웹 계층
    1. Web Layer
        1. 흔히 사용되는 컨트롤러와 JSP/Freemarker 등의 뷰 템플릿 영역
        2. 이외에도 필터, 인터셉터, 컨트롤러 어드바이스 등 외부 요청과 응답에 대한 전반적인 영역
    2. Service Layer
        1. @Service에 사용되는 서비스 영역
        2. 일반적으로 Controller와 Dao의 중간 영역
        3. @Transactional이 사용되는 영역
    3. Repository Layer
        1. Database와 같이 데이터 저장소에 접근하는 영역
        2. 기존 개발에 쓰이던 Data Access Object의 영역
    4. Dtos
        1. Dto는 계층간에 데이터 교환을 위한 객체이며 이들을 다루는 영역
    5. Domain Model
        1. 도메인이라 불리는 개발 대상을 모든 사람이 동일한 관점에서 이해할 수 있고 공유할 수 있도록 단순화 시킨 것을 도메인 모델이라고 한다.
        2. 예를 들면 택시 앱에서 배차,탑승,요금 등이 모두 도메인이 될 수 있다.
        3. @Entity가 사용된 영역 역시 도메인 모델
        4. 다만, 무조건 데이터베이스의 테이블과 관계가 있는 것은 아니다 (VO처럼 값 객체들도 이 영역에 해당)

* Entity 클래스가 있음에도 Dto를 만드는 이유
    1. Entity 클래스는 DB와 직접적으로 연결되는 핵심 클래스
    2. 이로 인해 Entity가 변경될 경우 다른 로직들이 큰 영향을 받는다.
    3. 따라서 View Layer와 DB Layer의 역할을 명확하게 분리하기 위해 Dto를 만든다.
    4. 자세한 설명 참조(https://gmlwjd9405.github.io/2018/12/25/difference-dao-dto-entity.html)
    
* JPA Auditing
    1. 보통 엔티티에서 해당 데이터의 생성시간과 수정시간을 포함한다. (차후 유지보수에 매우 중요)
    2. 그러나 매번 DB에 삽입 하기전 날짜 데이터를 등록/수정하는 코드가 여기저기 들어가게 되면서 코드가 지저분해지는 것을 해결하기 위해 JPA Auditing을 사용한다
    
* LocalDate
    1. Java8에서 나온 날짜타입으로 기존의 Date, Calendar클래스의 문제를 해결함
    2. Date의 문제점(불변객체가 아님, Calendar의 월(Month) 값)
    3. 스프링 부트 1.x에서는 별도의 Hibernate 5.2.10 버전 이상을 사용하도록 설정(2.x는 자동설정)

* 템플릿 엔진
    * 지정된 템플릿 양식과 데이터가 합쳐져 HTML 문서를 출력하는 소프트웨어(웹 사이트의 화면을 어떤 형태로 만들지 도와주는 양식)
    * 서버 사이드 템플릿 엔진
        1. 서버에서 Java 코드로 문자열을 만든 뒤 이 문자열을 HTML로 변환하여 브라우저로 전달
        2. 즉 서버에서 화면을 만들어 브라우저에게 전달한다.
        3. Spring + JSP, Freemarker 등
    * 클라이언트 사이드 템플릿 엔진
        1. 브라우저 위에서 작동(클라이언트에서 조립)
        2. 서버에서 Json 또는 Xml 형식의 데이터를 클라이언트에게 전달만 함
        3. SPA(Single Page Application) = Vue.js, React.js 등
        
* 머스테치(mustache)
    1. 수많은 언어를 지원하는 가장 심플한 템플릿 엔진
    2. Mustache.js와 Mustache.java 2가지가 있어, 하나의 문법으로 클라이언트/서버 템플릿을 모두 사용 가능
    3. 인텔리제이 커뮤니티 버전에서도 플러그인 사용 가능
    4. 파일의 기본 위치는 src/main/resources/templates
    5. 머스테치 스타터가 컨트롤러에서 문자열을 반환할 때 앞의 경로와 파일 확장자를 자동으로 지정
    
* header 와 footer
    1. HTML은 위에서부터 코드가 실행되기 때문에 head가 다 실행되고 나서 body가 실행된다.
    2. 즉 head가 다 불러지지 않으면 사용자 쪽에선 백지 화면만 노출
    3. 따라서 js의 용량이 크면 body부분의 실행이 늦어지기 때문에 js는 body하단에 두어 화면이 다 그려진 뒤 호출하는 것이 좋다.
    4. 반면 css는 화면을 그리는 역할이므로 head에서 불러 css가 적용된 화면을 볼 수 있게 한다.
    5. 추가로 bootstrap.js의 경우 제이쿼리가 꼭 있어야만 하기 때문에 부트스트랩보다 먼저 호출되도록 코드를 작성한다.    
    
* 스프링 시큐리티
    1. 막강한 인증과 인가 기능을 가진 프레임워크
    2. 스프링 기반의 애플리케이션에서는 보안을 위한 표준
    3. 인터셉터, 필터 기반의 보안 기능을 구현하는 것보다 스프링 시큐리티를 통해 구현하는 것을 적극적으로 권장
    
* 스프링 시큐리티 Oauth2 클라이언트
    1. 로그인 구현을 타 서비스에 맡기고 서비스 개발에 집중
    2. 1.5 -> 2.0 으로 연동방법이 크게 바뀌었으나 spring-security-oauth2-autoconfigure 라이브러리를 통해 1.5에서 쓰던 설정을 그대로 사용할 수 있다.
    3. 등록 방법
        1. 구글 서비스 등록(구글 클라우드 플랫폼에서 새로운 프로젝트를 만들고 OAuth 클라이언트 ID를 생성하여 ID와 보안비밀을 받는다)
        2. 프로젝트 내에 application-oauth.properties 에서 클라이언트 ID와 보안비밀을 등록한다.
        3. application.properties에서 oauth를 설정한다.
        4. application-oauth.properties의 값은 외부에 노출될 경우 언제든 개인정보를 가져갈 수 있는 취약점이 될 수 있으므로 gitignore에 등록하여 깃허브에 올라가는 것을 방지한다.
        5. 위의 절차를 통해 구글의 로그인 인증정보를 발급 받았으니 프로젝트에 User, Repository 등을 추가한다. 

* SecurityConfig 설정
    1. csrf().disable().headers().frameOptions().disable() : h2 콘솔 화면을 사용하기 위해 해당 옵션들을 disable 합니다.
    2. authorizeRequests (URL별 권한 관리를 설정하는 옵션의 시작점. 이것이 선언되어야만 antMatchers 옵션을 사용할 수 있다)
    3. antMatchers (권한 관리 대상을 지정하는 옵션, URL, HTTP 메소드별로 관리 가능, permitAll 을 통해 전체 열람 권한)
    4. anyRequest (설정된 값들 이외 나머지 URL, 여기서 authenticated를 통해 나머지 URL들은 모두 인증된 사용자들에게만 허용(인증된 사용자 = 로그인된 사용자))
    5. logout(로그아웃 기능에 대한 설정)
    6. oauth2Login (oauth2 로그인 기능에 대한 설정의 진입점)
    7. userInfoEndpoint (Oauth2 로그인 성공 이후 사용자 정보를 가져올 때의 설정)
    
* 어노테이션 만들기
    1. 세션값을 가져오는 SessionUser user = (SessionUser) httpSession.getAttribute("user") 부분을 어노테이션을 만들어 개선한다.
    2. @interface 를 통해 이 파일을 어노테이션 클래스로 지정한다.
    3. HandlerMethodArgumentResolver 인터페이스를 구현하여 조건에 맞는 경우 구현체가 지정한 값으로 해당 메소드의 파라미터를 넘길 수 있다.
    4. supportsParameter에서 특정 파라미터를 지원하는지 판단하고 resolveArgument 에서 파라미터에 전달할 객체를 생성한다.
    5. HandlerMethodArgumentResolver를 사용하려면 WebMvcConfigurer 를 구현하여 addArgumentResolvers가 있어야한다.
    6. @LoginUser SessionUser 를 사용할 수 있게 된다.
    
* 세션 저장소
    1. 기본적으로 세션은 WAS의 메모리(내장 톰캣의 메모리)에 저장된다. 그래서 애플리케이션을 재실행하면 항상 초기화가 된다.
    2. 현업에서 사용되는 세션 저장소
        * 톰캣 세션
            1. 별다른 설정을 하지 않았을 때 기본적으로 선택되는 방식
            2. 톰캣(WAS)에 세션이 저장되기 때문에 2대 이상의 WAS가 구동되는 환경에서는 톰캣들 간의 세션 공유를 위한 추가 설정이 필요
        * MySQL과 같은 데이터베이스 세션
            1. 여러 WAS간의 공용 세션을 사용할 수 있는 가장 쉬운 방법
            2. 많은 설정이 필요 없지만 로그인 요청마다 DB IO가 발생하여 성능상 이슈가 발생할 수 있다
            3. 보통 로그인 요청이 적은 백오피스, 사내 시스템 용도로 사용
        * Redis, Memcached와 같은 메모리 DB 세션
            1. B2C 서비스에서 가장 많이 사용하는 방식
            2. 실제 서비스로 사용하기 위해서는 Embedded Redis와 같은 방식이 아닌 외부 메모리 서버가 필요

## AWS 서버 환경 구축 - AWS EC2
* 클라우드(인터넷을 통해 서버,스토리지,데이터베이스,네트워크,모니터링 등의 서비스를 제공하는 것)
    1. Infrastructure as a Service(IaaS)
        1. 기존 물리 장비를 미들웨어와 함께 묶어둔 추상화 서비스
        2. 가상머신, 스토리지, 네트워크, 운영체제 등의 IT 인프라를 대여해주는 서비스
        3. AWS의 EC2,S3 등
    2. Platform as a Service(PaaS)
        1. IaaS에서 한번 더 추상화한 서비스
        2. 좀 더 많은 기능이 자동화
        3. AWS의 Beanstalk, Heroku 등
    3. Software as a Service(SaaS)
        1. 소프트웨어 서비스
        2. 구글드라이브, 드랍박스, 와탭 등
        
* AWS 환경 설정
    1. 회원가입 (AWS 공식 사이트에서 무료 계정 만들기)
    2. EC2 인스턴스 생성(지역 변경: 오하이오 -> 서울)
    3. 서비스에서 ec2 검색(인스턴스 시작: AMI 를 선택, 가상 머신에 운영체제를 설치할 때 구워넣는 이미지같은 것)
    4. 인스턴스 세부 정보 구성(프리티어 기준 : 인스턴스 1개, 30GB)
    5. 보안 그룹 설정
        * SSH 는 pem키가 있어야만 접속이 가능하기 때문에 pem키를 절대 노출해서는 안된다
        * IP또한 현재 접속한 IP에서만 설정, 다른 장소에 접속 할 경우 해당 장소의 IP를 다시 추가하는 것이 안전
        * 일종의 마스터키로 EC2 서버에 접속 할 때 필수 파일이니 잘 관리할 수 있는 디렉토리에 저장
    6. 고정 IP 주소 할당(탄력적 IP)
        * 탄력적 IP를 할당 받아 EC2주소와 연결(작업 -> 주소 연결)
        * 인스턴스 IP 확인을 통해 탄력적 IP가 잘 설정되었는지 확인
        * 탄력적 IP는 생성하고 EC2서버에 연결하지 않으면 비용이 발생
        * 즉, 생성한 탄력적 IP는 무조건 EC2에 바로 연결해야 하며 더 이상 사용할 인스턴스가 없을 때에는 탄력적 IP를 삭제해야 한다
    7. 윈도우에서 putty 설치
    8. puttygen.exe 에서 Conversions -> Import key를 통해 pem키를 ppk로 변환(Save Private Key)한다.
    9. putty를 실행하여 Session 에서 HostName(ec2-user@탄력적IP주소)과 Port(22) 를 설정, 현재 설정을 Save하여 저장
    10. putty 에서 Connection-SSH-Auth 에서 Browse.... 를 눌러 ppk파일을 로드
    
* EC2 환경 설정
    * Java 8 설치 : 현재 프로젝트와 맞는 버전을 설치해야한다. EC2 기본은 Java 7
        1. sudo yum install -y java-1.8.00-openjdk-devel.x86_64
        2. sudo /usr/sbin/alternatives --config java (2번 선택)
        3. sudo yum remove java-1.7.0-openjdk
        4. java --version
    * 타임존 변경 : 기본 시간대가 모두 미국 시간이므로 한국 시간으로 변경
        1. sudo rm /etc/localtime
        2. sudo ln -s /usr/share/zoneinfo/Asia/Seoul /etc/localtime
        3. date
    * 호스트네임 변경 : IP 주소로 출력되는 서버의 이름을 별명으로 변경한다(수십 대의 서버가 작동할경우 IP만으로 어떤 서버인지 알 수가 없기 때문)
        1. sudo vim /etc/sysconfig/network
        2. HOSTNAME 부분을 별명으로 변경
        3. sudo reboot
        4. sudo vim /etc/hosts
        5. 127.0.0.1 별명 (가장 아랫줄에 한줄 추가)
        6. curl 별명(Could not resolve host = 실패, Failed to Connect to = 성공)
            
* AWS RDS 환경 설정
    1. RDS 인스턴스 생성(검색창에 rds -> 데이터베이스 생성)
    2. 사용 DB 및 프리티어, 스토리지, DB사용자 이름과 암호(DB에 접근할 때 사용), 퍼블릭 액세스 기능 설정
    3. 필수 환경 설정
        * 타임존
            1. 파라미터 그룹 생성(동일한 DB버전을 설정)
            2. 생성된 파라미터를 클릭하여 파라미터 편집을 통해 time_zone 을 Asia/Seoul 로 변경
        * Character Set
            1. 파라미터 편집에서 character_set_client,connection,database,filesystem,results,server 를 모두 utf8mb4로 변경
            2. utf8과 utf8mb4의 차이는 이모티콘 저장 가능 여부(utf8mb4가 이모티콘 저장이 가능하다)
        * Max Connection
            1. 인스턴스 사양에 따라 자동으로 정해짐(보통 60)
            2. max_connections 를 검색하여 좀 더 넉넉한 값으로 변경(150 정도)
            3. 변경 사항 저장
    4. 데이터베이스 항목에서 db를 선택하여 수정을 통해 DB 파라미터 그룹을 방금 만든 파라미터로 변경
    5. 저장 후 반영 시점을 즉시 적용(예약일 경우 새벽 시간대에 진행)
    6. 데이터베이스 작업 -> 재부팅
    
* PC에서 RDS 접속
    1. RDS 세부정보 (DB명을 클릭)
    2. VPC 보안 그룹의 값을 클릭하여 EC2 보안 그룹 항목으로 이동(ec2 와 관련된 그룹 ID를 복사)
    3. RDS 세부정보에서 보안 그룹의 Inbound 항목을 클릭
        1. 인바운드 추가(MYSQL/Aurora, 사용자 지정, 복사된 ID)
        2. 현재 내 PC의 IP도 등록(MYSQL/Aurora, 내 IP)
        3. 이렇게 하면 EC2와 RDS 간에 접근이 가능
    4. 인텔리제이에서 Database 플러그인을 설치(Database Navigator)
    5. Action에서 Database Browser를 실행
    6. 플러스(+) 버튼을 클릭하여 MySQL 항목을 추가 RDS 접속 정보를 등록(이 때 Host는 RDS의 엔드포인트 주소)
    7. Test Connection에서 successful 이 뜨면 성공, Apply - OK
    8. Open SQL Console - New SQL Console 항목에서 새로운 콘솔창 생성(use DB명 실행 시 successfully 면 성공)
    9. show variables like 'c%'; 을 통해 설정 확인
    10. ALTER DATABASE 데이터베이스명 CHARACTER SET = 'utf8mb4' COLLATE = 'utf8mb4_general_ci'; 를 통해 설정 변경
    11. select @@time_zone, now() 를 통해 타임존 확인
    12. 마지막으로 테이블을 생성하고 insert 쿼리문을 실행하여 테스트
    13. putty 에서 EC2에 ssh접속을 하여 sudo yum install mysql 설치
    14. mysql -u 계정 -p -h Host주소(엔드포인트) 를 입력 후 패스워드 입력 - EC2에서 RDS로 접속
    15. show databases; 를 통해 RDS가 맞는지 확인 

* EC2로 프로젝트 배포
    1. sudo yum install git
    2. git --version
    3. mkdir ~/app && mkdir ~/app/step1
    4. cd ~/app/step1
    5. git clone 깃주소
    6. cd 프로젝트명
    7. ./gradlew test(실패 시 git pull 또는 chmod +x ./gradlew 를 통해 권한 추가)
    8. 배포 스크립트 작성(vim 편집기를 통해 배포와 관련된 명령어를 한번에 모아서 실행)
    9. chomd +x 를 통해 배포 스크립트에 실행 권한 추가
    10. 배포 스크립트 실행
    11. vim nohup.out 을 통해 정상적인 실행이 되었는지 확인(실패할 경우 가장 하단에 에러 출력)
    
    
    
## 오류 및 해결
* error: variable name not initialized in the default constructor private final String name (gradle 버전 문제, 5 -> 4로 다운 그레이드)
* org.h2.jdbc.JdbcSQLIntegrityConstraintViolationException: NULL not allowed for column "EMAIL"; SQL statement: (ofNaver 에서 구글과 다르게 response 로 값을 받아 처리)
* EC2 서버에서 deploy.sh 파일을 실행시켰을 때 springframework.security.oauth2.client.registreation.ClientRegistreationRepository 와 관련된 에러가 출력 (application-oauth.properties 와 같은 중요 파일의 누락, 하지만 git을 통해 올릴 수는 없기 때문에 EC2에서 직접 추가해서 관리한다. 그 후 deploy.sh에서 -Dspring.config.location을 통해 설정 파일의 위치를 지정한다)
* EC2에서 git clone을 통해 받은 파일을 build나 test를 통해 build파일이 추가되어 수정이 되었을 경우 git pull을 할 수가 없다. (병합이 불가능 하다는 메세지가 출력되어 삭제하고 git clone을 다시 함)
