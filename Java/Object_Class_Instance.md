
### ✔ 요약
> 객체(Object)는 구현할 대상입니다.  
> 클래스(Class)는 객체를 만들기 위한 설계도입니다.    
> 인스턴스(Instance)는 클래스 바탕으로 구현된 실체로 실제로 메모리에 할당된 객체입니다. 


## Object 오브젝트/객체
* 구현할 대상
* 각 객체는 `속성`과 `관련 행위 및 행동`을 갖고 있다. 
* *ex) 사람, 자동차, 건물, 주문, 게시글, ... 등*

## Class 클래스
* 객체(Object)를 만들어 내기 위한 **설계도** 혹은 틀
* "클래스를 정의한다." 👉 클래스로 객체의 속성과 기능을 코드로 구현한다. 

``` java
// 클래스
class Person {
    // 멤버 변수
    String name;
    int age;
    
    // 메서드, 멤버 함수
    void eat() {
        System.out.println("냠냠");
    }
}
```

## Instance 인스턴스
* 설계도 바탕으로 **구현된 구체적인 실체**
* "클래스를 인스턴스화하다." 👉 클래스를 바탕으로 객체를 실체화하다.
* 인스턴스는 객체이다.  
* OOP관점에서 객체가 **메모리에 할당**되어 실제 사용될 때 
* 어떤 원본(추상적인 개념)으로 부터 `생성된 복제본`을 의미한다.

``` java
class Main {
    public static void main(String[] args) {
        Person chaerin; // 객체
        
        chaerin = new Person(); // chaerin은 Person 클래스의 인스턴스 (객체를 메모리에 할당)
    }
}
```

* 자바는 다양한 방식으로 인스턴스를 만들 수 있는데 가장 간단한 방식은 `new` 예약어를 통해 생성자 호출하는 것이다.  
  * `new` 예약어를 통해 클래스 생성자를 호출하면 인스턴스가 **Heap 메모리에 생성된다.**
    * *Heap은 인스턴스 저장 공간으로 Garbage Collection 대상이다. JVM 성능 이슈에서 가장 많이 언급되는 공간이다.*
    * 자세한 내용은 JVM 메모리 구조 정리할 때 알아보자
  * `chaerin` 변수는 지역 변수이므로 **Stack 메모리**에 생성된다.
  * `chaerin` 변수에 **인스턴스의 메모리 주소 값(=해시코드 값)이 할당된다**. 인스턴스 메모리 주소 값을 참조하고 있기 때문에 `chaerin` 변수는 참조변수라고 부른다.


<br/><br/>

[참고1](https://mong9data.tistory.com/123)  
[참고2](https://gmlwjd9405.github.io/2018/09/17/class-object-instance.html)  