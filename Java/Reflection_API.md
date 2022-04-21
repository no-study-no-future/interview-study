# :coffee: Reflection API
스프링과 JPA를 공부하다보면 자바의 Reflection이라는 개념을 자주 접하게 되었다. Reflection API가 무엇인지 어느 상황에 사용되는지 알아보자.

**`Reflection`은 `런타임 환경`에서 Class, Method, Fields, Interfaces을 검사 혹은 수정할 수 있게 해주는 API**다.

이 기능은 다른 정적 언어 (C, C++, Pascal)에서는 제공하지 않는 Java만의 기능인데 런타임 상황에서 구체적인 클래스 정보를 알지 못할 때 활용할 수 있다고 한다 (사용자가 아는 것이 아니라 컴파일러 입장에서 구체적으로 정보를 알지 못할 때 이다). 대표적인 사용 예시로는 Dependency Injection, Proxy 등이 있다고 한다. 문장으로 보면 잘 이해가 가지 않으니 코드를 통해 살펴보자.

**Reflection을 이해하기 위한 단순 예제입니다.**
```Java
public class Item {
  private Long id;
  private String name;

  // Creating a public method with no arguments
  public void method1() {
    System.out.println("The string is " + name);
  }

  // Creating a public method with int as argument
  public void method2(Integer n) {
    System.out.println("The number is " + n);
  }

  // creating a private method
  private void method3() {
    System.out.println("Private method invoked");
  }
}
```

```Java
public static void main(String[] args) throws Exception {
    Class<?> itemClass = Item.class;

    System.out.println("==========필드 출력=============");
    for (Field field : itemClass.getDeclaredFields()) {
      System.out.println("field = " + field);
      field.setAccessible(true);
    }

    System.out.println("==========생성자 출력=========");
    for (Constructor<?> constructor : itemClass.getConstructors()) {
      System.out.println("constructor = " + constructor);
    }

    System.out.println("==========메소드 출력=========");
    for (Method method : itemClass.getMethods()) {
      method.setAccessible(true);
      System.out.println("method = " + method.getName());
      for (Class<?> parameterType : method.getParameterTypes()) {
        System.out.println("  parameterType = " + parameterType);
      }
    }
}
```

```
==========필드 출력=============
field = private java.lang.Long me.domo.java8.Item.id
field = private java.lang.String me.domo.java8.Item.name
==========생성자 출력=========
constructor = public me.domo.java8.Item()
==========메소드 출력=========
method = method1
method = method2
  parameterType = class java.lang.Integer
method = wait
  parameterType = long
method = wait
  parameterType = long
  parameterType = int
method = wait
method = equals
  parameterType = class java.lang.Object
method = toString
method = hashCode
method = getClass
method = notify
method = notifyAll

Process finished with exit code 0

```
Reflection API를 이용하면 접근 제한자 상관없이 메서드를 호출할 수 있다.

## 장점
- 확장성 : 정규화된 이름을 사용해 외부 사용자 정의 클래스를 사용할 수 있다.
- 클래스에 정의된 메서드를 이용해 높은 수준의 테스트가 가능하다

## 단점
- 성능 오버헤드 : 이미 생성된 인스턴스를 리플렉션 하는 것은 오버헤드를 일으키며, JVM 최적화가 적용되지 않습니다.
- 접근 제한자를 무시함으로써 캡슐화가 무의미해집니다.
- 컴파일때 확인되지 않는 문제가 발생할 수 있습니다.