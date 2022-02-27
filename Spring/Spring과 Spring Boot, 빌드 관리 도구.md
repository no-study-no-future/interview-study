# Spring과 Spring Boot, 빌드 관리 도구
## 요약
- Spring Framework란 자바 엔터프라이즈 개발을 편하게 해주는 오픈 소스 경량급 애플리케이션 프레임워크
- Spring Boot는 Spring Framwork를 사용하기 위한 설정을 자동화하여 사용자가 편하게 스프링을 활용할 수 있게 도움
- 빌드 관리 도구는 애플리케이션 개발 시 외부 라이브러리들을 관리해주는 것으로 Maven, Gradle 등이 있음

## Spring Framework란
- 자바 엔터프라이즈 개발을 편하게 해주는 오픈 소스 경량급 애플리케이션 프레임워크
- 특징
	- 경량 컨테이너 : 각각의 객체 생성, 소멸과 같은 라이프 사이클을 관리하며 스프링으로부터 필요한 객체를 얻어옴
		- 스프링 컨테이너 : 스프링의 빈(객체)을 관리
		- ApplicationContext(AnnotationApplicationContext)
	- IoC(제어의 역행) : 느슨한 결합, 제어권이 프레임워크에 있어 필요에 따라 스프링에서 사용자의 코드를 호출
	- DI(의존성 주입)를 통한 객체 간의 관계 구성 : 특정 객체가 필요로 하는 객체를 외부에서 결정(주입)(AppConfig)
	- AOP(관점지향 프로그래밍) 지원 : 공통적으로 사용하는 기능을 분리(Filter, Interceptor, AOP)

> Bean : Spring에 의해서 생성되고 관리되는 자바 객체  
> IoC, DI를 통해 OCP(개방폐쇄법칙)를 지킴 -> 스프링 프레임  

## Spring과 Spring Boot의 차이
### Spring의 문제점
- 기능이 많은 만큼 환경설정이 복잡
	- XML 설정 등
- Spring MVC, Hibernate 코어 및 Log4j와 같은 유사한 요구사항들의 중복

### Spring Boot
- 스프링 프레임워크를 사용하기 위한 설정의 많은 부분을 자동화
- 사용자가 편하게 스프링을 활용할 수 있도록 도움
- Spring 프레임워크와의 차이점
	- Starter를 통한 Dependency 자동화
	- Embed Tomcat으로 인한 Tomcat 설치 및 버전 관리 수고로움 X


## Maven, Gradle?
### 빌드 관리 도구
- 애플리케이션 개발 시 다양한 외부 라이브러리들이 필요
- 필요한 라이브러리들을 설정파일을 통해 자동으로 다운로드해주고, 관리해주는 도구

### Maven
- Apache Maven, 자바용 프로젝트 관리 도구
- 라이브러리들과 연관된 라이브러리들까지 모두 연동되어 관리
- POM(Project Object Model) 구조
	- pom.xml
	- 프로젝트 정보, 빌드 설정, 빌드 환경, pom 연관 정보

### Gradle
- 안드로이드 앱의 공식 빌드 시스템
- Maven에 비해 빌드 속도 10~100배 가량 빠름
- Java, C/C++, Python 등 지원
- 별도의 빌드 스크립트(Groovy)를 통하여 사용할 애플리케이션 버전, 라이브러리 등의 항목을 설정

### Maven, Gradle 차이점
- 가독성
	- pom.xml보다 build.gradle이 더 간결하고 가독성이 좋음
- 빌드 시간 단축
		- Gradle의 경우 이미 업데이트된 테스크에 대해 작업이 실행되지 않는 incremental build를 허용하기에 빌드 시간이 단축
- 대규모 프로젝트
	- 가독성, 빌드 시간 단축 등의 이유로 대규모 프로젝트일 경우 Gradle이 더 적합하다고 함

## 출처
- [번역글 스프링 vs 스프링 부트 차이 비교하기!](https://sas-study.tistory.com/274)
- [Spring 과 Spring Boot 차이](https://velog.io/@courage331/Spring-%EA%B3%BC-Spring-Boot-%EC%B0%A8%EC%9D%B4)
- [Spring 스프링 프레임워크(Spring Framework)란?](https://steady-coding.tistory.com/457)
- [메이븐(Maven)과 그래들(Gradle)의 개념 및 비교 :: 슬기로운 개발생활😃](https://dev-coco.tistory.com/65)