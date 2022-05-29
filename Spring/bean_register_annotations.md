# Spring Bean 등록 시 사용되는 Annotation 비교

기존 Spring MVC에서는 xml을 활용하여 Bean을 등록하고 있었습니다.  
하지만 프로젝트 규모가 커짐에 따라 사용하는 요소들을 xml에 등록하는 것이 상당히 번거로워져`Annotation(@)`을 활용한 Bean 등록 방법이 생기게 되었습니다.  
Bean 등록할 때 활용 가능한 `@Bean`, `@Component`, `@Configuration`에 대해서 알아봅시다. 😎


## 🤔 Spring Bean?
Spring에서는 Spring의 DI Container에 의해 관리되는 POJO (Plain Old Java Object)를 Bean이라고 부릅니다. 
이 Bean들은 Spring을 구성하는 핵심 요소입니다.
- POJO로써 Spring 애플리케이션을 구성하는 핵심 객체
- Spring IoC 컨테이너 (DI 컨테이너)에 의해 생성 및 관리됨
- 주요 속성으로 class, id, scope, constructor-arg 등

### Spring Bean의 구성요소
- class: Bean으로 등록할 Java 클래스
- id: Bean의 고유 식별자
- scope: Bean을 생성하기 위한 방법 (singleton, prototype 등)
- constructor-arg: Bean 생성 시 생성자에 전달할 파라미터
- property: Bean 생성 시 setter에 전달할 인수

Spring에서는 등록되어 있는 Bean을 싱글톤 객체로 생성하여 관리합니다.  
그래서 Bean을 등록하기 위한 다양한 방법들을 제공하고 있는데 각각 방법들에 대해 살펴봅시다. 😎



## Spring Bean 등록 방법
## 1. @Bean + @Configuration
예를 들어 다음과 같은 클래스를 Spring Container에 등록한다고 가정해봅시다.
``` java
public class ChallengeClass {

}
```
이 클래스를 Bean으로 등록하기 위해서는 설정 클래스에서 `@Bean`을 사용하여 **수동으로 Spring Container에 Bean을 등록할 수 있습니다.**
설정 클래스에는 `@Configuration`을 꼭!! 붙여주어야 합니다. (그래야 싱글톤 보장이 됩니다.)  
이렇게 수동으로 등록된 Bean은 **메소드 이름으로 Bean 이름이 결정**되므로 중복되지 않도록 주의해야 합니다.  

``` java
@Configuration
public class ClassConfig {
    @Bean
    public ChallengeClass challengeClass() {
        return new ChallengeClass();
    }
}
```
Spring Container는 `@Configuration`이 붙어있는 클래스를 자동으로 Bean으로 등록해두고 해당 클래스를 파싱해서 `@Bean`이 있는 메소드를 찾아 Bean을 생성해줍니다.  
  
`@Bean`을 주로 사용하는 경우는 아래와 같습니다.
- ✅ 개발자가 직접 제어가 불가능한 라이브러리를 활용할 때
- ✅ 애플리케이션 전범위적으로 사용되는 클래스를 등록할 때
- ✅ 다형성(polymorphism)을 활용하여 여러 구현체를 등록해주어야 할 때

ex)  
- 객체를 json으로 변경할 때 gson 라이브러리를 사용할 때 싱글톤 Bean으로 등록해주어 1개의 객체만 생성하여 여러 클래스가 공유함으로써 메모리 상의 이점을 얻을 수 있다. 
  우리가 만든 클래스가 아니므로 불가피하게 `@Bean`으로 등록해줘야 한다.
- 여러 구현체를 Bean으로 등록할 때 어떤 구현체들이 Bean으로 등록되는지 `@Configuration` 클래스만 보고 한 눈에 파악하여 유지보수 하기 좋다.


* `@Configuration` @interface
``` java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Component
public @interface Configuration {
    // ...
}
```


## 2. @Component
위와 같은 방식은 Bean으로 등록해야 하는 클래스가 많아질 수록 상당히 많은 시간이 걸립니다.    
그래서 Spring에서는 특정 어노테이션이 있는 클래스를 찾아서 Bean으로 등록해주는 컴포넌트 스캔 기능을 제공합니다.  
컴포넌트 스캔은 `@Component`가 있는 클래스들을 찾아 자동으로 Bean으로 등록해줍니다.  
_(컴포넌트 스캔: Bean으로 등록 될 준비를 마친 클래스들을 스캔하여, Bean으로 등록해주는 것)_  

그래서 우리가 직접 개발한 클래스를 빈으로 편리하게 등록하고자 하는 경우에는 `@Component`를 활용하면 됩니다.
``` java
@Component
public class ChallengeClass {

}
```
`@Component`를 갖는 어노테이션은 `@Controller`, `@Service`, `@Repository`등이 있으며 `@Configuration` 역시 갖고 있습니다.  
`@Configuration`안에 있는 `@Component`에 의해 설정 클래스 역시 자동으로 Bean으로 등록되고 `@Bean`이 있는 메소드를 통해 Bean 등록을 할 수 있었던 것입니다.

* `@Component` @interface
``` java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Indexed
public @interface Component {
    // ...
}
```
`@Component`를 이용할 경우 Main 클래스에서 @ComponentScan으로 컴포넌트 찾는 탐색 범위를 지정해주어야 합니다. (ex. `@ComponentScan(basePackages = "com.test.xxx")`)  
하지만 SpringBoot를 이용중이라면 `@SpringBootConfiguration` 하위에 기본적으로 포함되어 있어 별도 설정이 필요없습니다.


> Spring은 기본적으로 **컴포넌트 스캔을 이용한 자동 Bean 등록 방식을 권장**합니다.
> 왜냐하면 해당 Bean이 잘 설정되어 있는지 바로 파악할 수 있기 때문입니다.
> 또한 `@Bean`을 이용하면 Bean으로 등록해야 하는 작업과 Bean이 등록되었는지 파악하는 과정도 번거로워질 수 있습니다.  
> `@Bean`으로 등록해야 하는 경우를 제외한 나머지의 경우에는 자동 등록 방식을 사용합시다!


## @Bean + @Configuration vs. @Component
### @Bean + @Configuration
- 수동으로 Spring Container에 Bean을 등록하는 방법
- 개발자가 직접 제어 불가능한 library를 Bean으로 등록할 때 사용
- 유지보수성을 높이기 위해 애플리케이션 전범위적으로 사용되는 클래스나 다형성을 활용하여 여러 구현체를 빈으로 등록할 때 사용
- 1개 이상의 `@Bean`을 제공하는 클래스의 경우 반드시 `@Configuration`을 명시해 주어야 싱글톤이 보장됨

### @Component
- 자동으로 Spring Container에 Bean을 등록하는 방법
- Spring의 Component Scan 기능이 `@Component`가 있는 클래스를 자동으로 찾아서 Bean으로 등록함
- 대부분의 경우 `@Component`를 이용한 자동 등록 방식 사용을 추천함
- `@Component`의 하위 어노테이션: `@Configuration`, `@Controller`, `@Service`, `@Repository` 등


<br/><br/><br/><br/><br/>

* Ref.
  * [[Spring] 빈 등록을 위한 어노테이션 차이 및 비교](https://mangkyu.tistory.com/75)