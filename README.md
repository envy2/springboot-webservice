# Springboot Webservice (2020.07.20 ~ )
AWS 와 Springboot를 사용하여 웹 서비스 구현

## Terminal
gradlew wrapper --gradle-version 4.10.2

## Library
* JUnit4
* Lombok

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


