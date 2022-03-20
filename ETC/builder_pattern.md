
# Builder Pattern 빌더 패턴
* 클래스에 생성자(`constructor`) 인자가 많다면 `builder pattern`을 사용하면 좋다.
* 인스턴스 생성할 때, 생성자만을 통해서 생성하는데 어려움이 있어 고안된 패턴

<br/>
<br/>

``` java
public class User {
    private int userId;     // 필수
    private String name;    // 필수
    private int age;        // 선택
    private String address; // 선택
    private String email;   // 선택
}
  ```
* 빌더 패턴을 사용하지 않는 경우, 필수인자만 받는 생성자를 만들고 선택적으로 받는 생성자를 만드면 된다.
  * `overloading`을 통한 생성자 여러 개 만들기  
  👉 점층적 생성자 패턴 (`telescoping constructor pattern`)

<br/>

### 점층적 생성자 패턴 (telescoping constructor pattern)

``` java
public class User {
    private String userId;  // 필수
    private String name;    // 필수
    private int age;        // 선택
    private String address; // 선택
    private String email;   // 선택
    
    public User(String userId, String name) {
        this.userId = userId;
        this.name = name;
    }

    public User(String userId, String name, int age) {
        this(userId, name);
        this.age = age;
    }
    
    public User(String userId, String name, int age, String address) {
        this(userId, name, age);
        this.address = address;
    }
    
    public User(String userId, String name, int age, String address, String email) {
        this(userId, name, age, address);
        this.email = email;
    }
}
  ```
* 코드가 길어지고 지저분해진다.

``` java
User user = new User("user_12345", "사용자");
User user = new User("challenger", "챌린지", 11);
User user = new User("challenger", "챌린지", 11, "경기도");
User user = new User("challenger", "챌린지", 11, "경기도", "abc@def.com");
```
* 순서를 알고 있어야 한다.  
* 생성자 인자가 많은 경우 알아보기가 힘들다.  

<br/>

이러한 단점을 보완하기 위해 `setter` 메소드를 사용한 `자바 빈(Bean) 패턴`이 고안되었다.

### 자바 빈(Bean) 패턴

``` java
User user = new User();
user.setUserId("challenger");
user.setName("챌린지");
user.setAge(11);
user.setAddress("경기도");
user.setEmail("abc@def.com");
```
* 객체 생성하기 쉬우며 읽기 좋다.  
* 한 줄 작성으로 객체 생성이 끝나지 않아 객체 일관성(consistency)이 일시적으로 깨지는 위험 발생  
  👉 thread간 공유 가능한 상태가 존재하기 때문  


<br/>

생성자 패턴과 자바 빈 패턴의 장점을 결합한 것이 바로 빌더 패턴(`builder pattern`)이다.

### 빌더 패턴 (Builder Pattern)

* 필요한 객체를 직접 생성하는 대신 먼저 *필수 인자*들을 생성자에 전부 전달하여 빌더 객체를 만든다. 
* *선택 인자*는 가독성이 좋은 코드로 인자를 넘길 수 있다.
* 객체 일관성이 유지된다.

``` java
public class User {
    private String userId;  // 필수
    private String name;    // 필수
    private int age;        // 선택
    private String address; // 선택
    private String email;   // 선택

    // static 클래스인 Builder 존재 - 내부에 빌더 클래스로 선언할 경우 꼭 static으로 선언!
    public static class Builder {
        private String userId = "challenger";
        private String name = "챌린지";
        private int age = 11;
        private String address = "경기도";
        private String email = "abc@def.com";

        // 필수 값인 userId와 name 인자를 가진 생성자
        public User(String userId, String name) {
            this.userId = userId;
            this.name = name;
        }

        public Builder age(int age) {
            this.age = age;
            return this;
        }
       
        public Builder address(String address) {
            this.address = address;
            return this;
        }
        
        public Builder email(String email) {
            this.email = email;
            return this;
        }

        // 빌더 클래스 반환
        public User build() {
            return new User(this);
        }
    }

    public User(Builder builder) {
        this.userId = builder.userId;
        this.name = builder.name;
        this.age = builder.age;
        this.address = builder.address;
        this.email = builder.email;
    }

    public static void main(String[] args) {
        User user = new Builder("challenger", "챌린지")
                .age(11)
                .address("경기도")
                .email("abc@def.com")
                .build();
    }
}
```

* User 클래스에 setter 는 없이 getter 만 갖고 있으며 public 생성자가 따로 없다.   
  👉 User 객체를 얻기 위해서는 오직 Builder 클래스를 통해서만 가능하다.
* 빌더 패턴은 만들려는 객체를 바로 만들지 않고 클라이언트가 **빌더(생성자 또는 static 팩토리)에 필수적인 매개변수**를 주면서 
호출해 `Builder` 객체를 얻는다.
* 그 다음 빌더 객체가 제공하는 **`setter`와 비슷한 메소드를 사용해서 부가적인 필드**를 채운다. 
* 최종적으로 **`build`라는 메소드를 호출**해서 만드려는 **객체를 생성**한다.

<br/><br/>

> ## ✔ 정리
> ### Builder Pattern의 장점
> 
> - 불필요한 생성자를 만들지 않고 객체를 만든다.  
> 👉 필요한 데이터만 설정할 수 있다.
> - 데이터 순서 상관없이 객체를 만들 수 있다.   
> 👉 매개변수에 따라 다른객체 만들 수 있다. 유연하다.
> - 사용자가 봤을 때 명시적이고 가독성이 좋다.
> - 변경 가능성 최소화할 수 있다.

<br/>
<br/>

[참고1](https://devlog-wjdrbs96.tistory.com/207)  
[참고2](https://mangkyu.tistory.com/163)  