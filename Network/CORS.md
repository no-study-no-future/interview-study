# CORS (Cross-Origin Resource Sharing)

## 들어가면서
웹 어플리케이션을 개발하다보면 CORS문제와 마주하는 경우가 있다. 쉽게는 로컬에서 작업할 때 뷰나 리액트 같은 Single Page Applicagion(SPA)로 프론트를 구성할 때 맞이할 수 있다.   
서버가 JAVA-Spring로 만들었다고 가정할 때 가장 단순한 해결법에는 2가지가 있다. (참고로 스프링 프레임워크 4.2부터 CORS를 지원했다.)   
[코드 출처 : [Spring Blog - CORS support in Spring Framework](https://spring.io/blog/2015/06/08/cors-support-in-spring-framework)]
1. @CrossOrigin 어노테이션 사용하기
    - Method level
        ```java
        @RestController
        @RequestMapping("/account")
        public class AccountController {

            @CrossOrigin
            @GetMapping("/{id}")
            public Account retrieve(@PathVariable Long id) {
                // ...
            }

            @DeleteMapping("/{id}")
            public void remove(@PathVariable Long id) {
                // ...
            }
        }
        ```
    - Class Level
        ``` java
        @CrossOrigin(origins = "http://domain2.com", maxAge = 3600)
        @RestController
        @RequestMapping("/account")
        public class AccountController {

            @GetMapping("/{id}")
            public Account retrieve(@PathVariable Long id) {
                // ...
            }

            @DeleteMapping("/{id}")
            public void remove(@PathVariable Long id) {
                // ...
            }
        }
        ```
2. Global configuration
    - Java config
        ``` java
        // Spring framework
        @Configuration
        @EnableWebMvc
        public class WebConfig extends WebMvcConfigurerAdapter {

            @Override
            public void addCorsMappings(CorsRegistry registry) {
                registry.addMapping("/**");
            }
        }

        // Spring boot
        @Configuration
        public class MyConfiguration {

            @Bean
            public WebMvcConfigurer corsConfigurer() {
                return new WebMvcConfigurerAdapter() {
                    @Override
                    public void addCorsMappings(CorsRegistry registry) {
                        registry.addMapping("/**");
                    }
                };
            }
        }

        // CORS Configuration override
        @Override
        public void addCorsMappings(CorsRegistry registry) {
            registry.addMapping("/api/**")
                .allowedOrigins("http://domain2.com")
                .allowedMethods("PUT", "DELETE")
                    .allowedHeaders("header1", "header2", "header3")
                .exposedHeaders("header1", "header2")
                .allowCredentials(false).maxAge(3600);
        }
        ```
    - XML
        ```xml
        <mvc:cors>
            <mvc:mapping path="/api/**"
                allowed-origins="http://domain1.com, http://domain2.com"
                allowed-methods="GET, PUT"
                allowed-headers="header1, header2, header3"
                exposed-headers="header1, header2" allow-credentials="false"
                max-age="123" />

            <mvc:mapping path="/resources/**"
                allowed-origins="http://domain1.com" />
        </mvc:cors>
        ```
        스프링 시큐리티를 사용할경우에는 방법이 좀 다르다.

## 정의
추가 HTTP 헤더를 사용하여 실행 중인 어플리케이션이 다른 출처의 자원에 접근할 수 있는 권한을 부여하도록 브라우저에 알려주는 시스템. 웹 어플리케이션은 자원이 **자신의 출처(도메인, 프로토콜, 포트)**와 다를 때 CORS HTTP 요청을 실행합니다.
[[출처](https://developer.mozilla.org/ko/docs/Web/HTTP/CORS)]

## 작동
![cors_principle](./assets/cors_principle.png)   
CORS는 웹 브라우저에서 특정 자원에 대한 접근을 허가 받기 위해 본인의 출처를 서버에 설명하는 HTTP 헤더(OPTIONS)를 통해 작동합니다. 브라우저는 서버에 요청을 보내기전에 preflight(사전 전달) 패킷을 전송하고 서버에서 **허가** 응답을 보냈을 때 실제 요청을 전송합니다.

대부분 CORS 문제가 발생한 경우 아래와 같이 콘솔에 출력될 것 입니다.
~~ 스터디 끝나고 재현해서 추가할게요 ㅠㅠ~~

## Simple request
**simple request**로 분류되는 요청은 preflight을 전송하지 않습니다. (Fetch API 명세에서는 이 용어를 사용하지는 않지만 많은 사이트에서 해당 용어를 사용했기에 사용하도록 하겠습니다.)    
Simple request은 아래 조건을 모두 충족한 경우에 해당됩니다.
- Method
    - GET
    - POST
    - HEAD
- HEADER
    - Accept
    - Accept-Language
    - Content-Language
    - Content-Type
        - application/x-www-form-urlencoded
        - multipart/form-data
        - text/plain
- [ReadableStream](https://developer.mozilla.org/ko/docs/Web/API/ReadableStream) 객체 미사용