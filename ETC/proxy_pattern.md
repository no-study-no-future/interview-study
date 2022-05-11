# 프록시 패턴 (Proxy Pattern)

### ✔ 요약

> 프록시는 다른 무언가와 이어지는 인터페이스의 역할을 하는 클래스를 의미한다.   
> 
> **인터페이스를 사용하고 실행시킬 클래스에 대한 객체가 들어갈 자리에 대리자 객체를 대신 투입한다.**   
> 객체를 사용하고자 할 때, 직접적으로 참조하는 것이 아닌 해당 객체를 대행하는 객체를 통해 접근하는 방식을 사용하게 되면 
> 해당 객체가 메모리에 존재하지 않아도 기본적인 정보를 참조하거나 설정할 수 있고 
> 실제 객체의 기능이 필요한 시점까지 객체의 생성을 미룰 수 있다.
> 
> 흐름제어만 할 뿐 결과값을 조작하거나 변경하면 안된다.  
> 
> **흐름제어는 왜 필요할까?**   
> 배틀그라운드 게임 시, 사용자가 죽고 나서 다시 살아나는 동안 로딩시간이 걸린다. 
> 데이터가 큰 이미지나 그래픽을 로딩하는데 시간이 걸리는데 로딩될 때까지 아무 거도 못하고 기다려야 한다. 
> 프록시는 제어 흐름을 통해 큰 데이터가 로딩될 때 까지 현재까지 완료된 걸 우선적으로 보여준다.  


<br/><br/>

프록시는 대리인이라는 뜻으로 **무언가를 대신 처리**하는 의미이다. 
사장님한테 사소한 질문을 하기보다는 비서에게 먼저 물어보는 개념이라고 생각하자.

이렇게 어떤 객체를 사용하고자 할 때, 객체를 직접적으로 참조하는 게 아닌 해당 객체를 대행`대리, proxy`하는 객체를 통해 
대상객체에 접근하는 방식을 사용하면 **해당 객체가 메모리에 존재하지 않아도 기본적인 정보를 참조하거나 설명할 수 있다. 
실제 객체의 기능이 반드시 필요한 시점까지 객체의 생성을 미룰수 있다.**    



예를 들어 용량이 큰 이미지와 글이 같이 있는 문서를 모니터 화면에 띄울 때 이미지 용량은 크고 텍스트 용량은 작아서 
텍스트는 빠르게 나타나지만 이미지는 느리게 로딩되는 걸 본 적이 있을 것이다. 
만약 이렇게 처리가 안되고 이미지와 텍스트가 모두 로딩이 된 후 화면이 나온다면 사용자는 로딩이 될 때까지 기다려야 한다. 
그러므로 먼저 로딩이 끝나는 텍스트라도 먼저 나오는 게 좋다. 
이런 방식을 취하려면 텍스트 처리용 프로세서, 이미지 처리용 프로세스를 별도로 운영하면 된다. 
이런 구조를 `프록시 패턴 (Proxy Pattern)` 이라고 한다. 
일반적으로 프록시는 다른 무언가와 이어지는 인터페이스의 역할을 하는 클래스를 의미한다.  


![proxy](https://github.com/ChaerinYu/Today-I-Learned/blob/main/DesignPattern/images/proxy.png?raw=true)  
- 클라이언트가 어떤 일에 대하여 요청하면 `Proxy`가 대신 `RealSubject`의 `request()` 메서드 호출을 하고 그 반환 값을 클라이언트에게 전달한다.


## 프록시가 사용되는 대표적인 3가지

### 가상프록시

- 필요로 하는 시점까지 객체 생성을 연기하고 해당 객체가 생성된 거처럼 동작하도록 만들고 싶을 때 사용하는 패턴. 
- 프록시 클래스에서 자잘한 작업들을 처리하고 리소스가 많이 요구되는 작업들이 필요할 때에만 주체 클래스를 사용하도록 구현한다. 

- ex) 해상도가 아주 높은 이미지를 처리해야 하는 경우 작업을 분산하는 것

 

### 원격프록시

- 원격 객체에 대한 접근이 제어 로컬 환경에 존재하며 원격 객체에 대한 대변자 역할을 하는 객체
- 서로 다른 주소 공간에 있는 객체에 대해 같은 주소 공간에 있는 거처럼 동작하게 만드는 패턴
- ex) Google Docs - 브라우저는 브라우저대로 필요한 자원을 로컬에 갖고 있고 또 다른 자원은 Google 서버에 있는 형태



### 보호프록시

- 주체 클래스에 대한 접근을 제어하기 위한 경우에 객체에 대한 접근 권한을 제어하거나 객체마다 접근 권한을 다르게 하고 싶을 때 사용하는 패턴
- 프록시 클래스에서 클라이언트가 주체 클래스에 대한 접근을 허용할지 말지 결정할 수 있다.





## 장점

1. 사이즈가 큰 객체가 로딩되기 전에도 프록시를 통해 참조할 수 있다.
2. 실제 객체의 `public`, `protected` 메소드들을 숨기고 인터페이스를 통해 노출시킬 수 있다.
3. 로컬에 있지 않고 떨어져 있는 객체를 사용할 수 있다.
4. 원래 객체의 접근에 대해서 사전처리할 수 있다.



## 단점

1. 객체를 생성할 때 한 단계를 더 거치게 되므로 빈번한 객체 생성 시 성능 저하될 수 있다.
2. 프록시 내부에서 객체 생성을 위해 스레드가 생성, 동기화가 구현돼야 하는 경우 성능 저하될 수 있다.
3. 로직이 난해해 가독성이 떨어질 수 있다.





### 프록시 패턴 예시

```java
interface Image {
    public void displayImage();
}

//on System A
class RealImage implements Image {
    private String filename;
    public RealImage(String filename) {
        this.filename = filename;
        loadImageFromDisk();
    }

    private void loadImageFromDisk() {
        System.out.println("Loading   " + filename);
    }

    @Override
    public void displayImage() {
        System.out.println("Displaying " + filename);
    }
}

//on System B
// 대리자 객체
class ProxyImage implements Image {
    private String filename;
    private Image image;

    public ProxyImage(String filename) {
        this.filename = filename;
    }

    @Override
    public void displayImage() {
        if (image == null)
           image = new RealImage(filename);

        image.displayImage();
    }
}

class ProxyExample {
    public static void main(String[] args) {
        Image image1 = new ProxyImage("HiRes_10MB_Photo1");
        Image image2 = new ProxyImage("HiRes_10MB_Photo2");

        image1.displayImage(); // loading necessary
        image2.displayImage(); // loading necessary
    }
}
```

-  결과

```
Loading    HiRes_10MB_Photo1
Displaying HiRes_10MB_Photo1
Loading    HiRes_10MB_Photo2
Displaying HiRes_10MB_Photo2
```

[예시출처](https://ko.wikipedia.org/wiki/%ED%94%84%EB%A1%9D%EC%8B%9C_%ED%8C%A8%ED%84%B4)    

- `ProxyExample` 클래스에서 `RealImage` 클래스에 직접 접근하지 않고 `ProxyImage` 클래스에서 객체를 생성하여 대신 일을 수행하고 있다. 
  - `ProxyImage`는 `RealImage`의 인스턴스를 가지고 있으면서 직접 `RealImage`의 `displayImage()` 메서드를 호출하고 그 반환값을 요청자에게 넘겨준다.

- 인터페이스를 중간에 두어 구체클레스들에게 영향을 받지 않게 설계했다. 👉🏻 OCP
- 직접 접근하지 않고 Proxy를 통해서 한 번 더 우회해서 접근하고 있다. 👉🏻 DIP


<br/><br/>




[출처1](https://coding-factory.tistory.com/711)  
[출처2](https://limkydev.tistory.com/79)   