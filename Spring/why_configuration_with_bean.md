# @Bean이 @Configuration 안에서 사용되어야 하는 이유
`@Bean`을 이용한 수동 Bean 메소드는 Spring Bean 안에만 구현되어 있다면 모두 동작합니다.  
하지만 Spring은 `@Bean`이 반드시 `@Configuration`을 활용하도록 강조하는데 그 이유는 `@Configuration`에 특별한 부가 기능이 적용되기 때문입니다.  

## @Configuration에 적용되는 Proxy Pattern
`@Configuration`은 `@Component`의 하위 어노테이션으로 `@Configuration`이 붙어있는 클래스 역시 Spring Bean으로 등록됩니다.   
그럼에도 Spring이 `@Configuration`을 따로 만든 이유는 **CGLib으로 Proxy Pattern을 적용해 수동으로 등록하는 Bean이 반드시 싱글톤으로 생성됨을 보장**하기 위해서입니다.  

예를 들어 다음과 같이 Spring Bean으로 등록하고자 하는 클래스가 있다고 합시다.
``` java
public class TestResourceManager {

}
```
``` java
@Configuration
public class MyConfig {

    @Bean
    public TestResourceManager testResourceManager() {
        return new TestResourceManager();
    }
    
    @Bean
    public FirstBean firstBean() {
        return new FirstBean(testResourceManager());
    }
    
    @Bean
    public SecondBean secondBean() {
        return new SecondBean(testResourceManager());
    }
}
```
위와 같이 Bean을 생성하는 메소드를 여러 번 호출한다면 불필요하게 여러 개의 Bean이 생성됩니다.  
이러한 문제를 방지하기 위해 Spring은 `@Configuration`이 있는 클래스를 객체로 생성할 때 CGLib 라이브러리를 사용해 Proxy Pattern을 적용합니다.  
그래서 **`@Bean`이 있는 메소드를 여러 번 호출하여도 항상 동일한 객체를 반환하여 싱글톤을 보장**합니다.

이를 이해하기 쉬운 코드로 나타내면 다음과 같습니다. (실제로는 이렇지 않으니 이해를 돕기 위한 코드로 봐주세요.)
``` java
@Configuration
public class MyConfigProxy extends MyConfig { 

    private Object source;

    @Override
    public TestResourceManager testResourceManager() {
        if (testResourceManager == null) {
            source = super.testResourceManager();
        }
        
        return source; 
    } 
    
    @Override
    public FirstBean firstBean() {
        return super.firstBean();
    } 
    
    @Override
    public SecondBean secondBean() {
        return super.secondBean();
    } 
}
```

## 싱글톤 여부를 제어하기 위한 proxyBeanMethods
대부분의 경우에는 `@Bean`에 의한 수동 빈 등록을 할 때 싱글톤으로 생성되기를 원합니다.   
하지만 @Bean 메소드를 호출할 때 의도적으로 매번 다르 객체가 생성되기 원할 때에는 proxyBeanMethods를 false로 주면 됩니다.  

``` java
@Configuration(proxyBeanMethods = false)
public class MyConfig {
    // ...
}
```
위의 설정 클래스에 대해서는 proxy가 적용되지 않으며 모든 @Bean 메소드 호출마다 새로운 객체를 생성해줍니다.  
하지만 거의 모든 상황에서 새로운 객체의 생성을 필요로 하는 경우는 거의 없습니다.  



`@Configuration`이 아니라면 Bean이 싱글톤임을 보장받을 수 없으므로 반드시 `@Bean` 사용 시 클래스에 `@Configuration`를 적용합시다!!




<br/><br/><br/><br/><br/>

* Ref.
    * [[Spring] @Configuration안에 @Bean 사용 이유, proxyBeanMethods](https://mangkyu.tistory.com/234)