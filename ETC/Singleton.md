
# Singleton Pattern 싱글톤 패턴

### ✔ 요약
> 똑같은 인스턴스는 만들지 않고 **하나의 인스턴스만 생성하여 사용**하는 디자인 패턴  
> 매번 객체를 생성하는 것이 자원의 낭비가 되므로 **하나를 만들어서 같이 사용**하기 위해 사용된다.  
 

자바에서는 생성자를 `private`으로 선언하여 `getInstance()` 메소드를 통해 받아서 사용하도록 구현한다.  
👉 `private` 생성자, `static` 필드

* Eager Initialization
``` java
// 싱글톤의 가장 기본적인 이른 초기화 방식 (Eager initialization)
// 장점: 클래스 로더에 의해 클래스 로딩될 때 싱글톤객체가 생성된다. (Thread-safe)
// 단점: 사용유무 관계없이 싱글톤 객체가 생성되어 메모리를 잡고 있어 비효율적일 수도 있다.  
public class EagerInitialization  {
  // private static으로 선언
  private static EagerInitialization instance = new EagerInitialization ();
  
  // 생성자
  private EagerInitialization () {}
  
  // 인스턴스 리턴
  // 정적메서드인 getInstance() 메서드를 이용해서 인스턴스 접근이 가능하다.
  public static EagerInitialization getInstance() {
    return instance;
  }
  
}
```
* Lazy Initialization
  * `Eager Initialization`과 정반대로, 클래스의 인스턴스가 사용되는 시점에서 인스턴스를 생성한다.
  * 사용하기 전까지는 메모리를 점유하지 않는다.
``` java
  // Lazy Initialization (게으른 초기화)
  // 장점: 싱글톤 객체가 필요할 때 인스턴스를 얻을 수 있다. 메모리 누수 방지
  // 단점: multi-thread환경에서 동시에 getInstance() 호출 시, 인스턴스가 두 번 생성된다.
  public class LazyInitialization {

      private static LazyInitialization instance;
  
      private LazyInitialization(){}
      
      public static LazyInitialization getInstance(){
          if(instance == null){
              instance = new LazyInitialization();
          }
          return instance;
  }

}
```

일반적인 변수들은 `new`를 통해 객체를 생성하게 되는데, 매번 메모리 영역을 할당받는다.  
싱글톤을 사용하는 경우, 한 번의 `new`를 통해 객체를 생성하므로 **메모리 낭비를 방지**할 수 있다.  
싱글톤으로 구현한 인스턴스는 '전역'이므로 다른 클래스의 인스턴스들이 **데이터를 공유**하는 것이 가능하다.  

### 장점
* 메모리 낭비 방지
* 데이터 공유 가능
* 두 번째 이용부터는 객체 로딩시간 현저히 줄어 성능 좋아짐

### 문제점
* 의존 관계 상 클라이언트가 구체 클래스에 의존한다.    
👉 OCP(개방폐쇄원칙: 확장엔 열려있고 수정엔 닫혀있어야 한다.) 위배, 높은 결합도   
👉 유지보수 힘들고 테스트도 힘들다.  
* private 생성자로 테스트가 어렵다.
* 가비지 컬렉팅이 되지 않아 프로그램 종료할 때 까지 메모리를 차지한다.  
* 멀티스레드 환경에서 동기화 처리하지 않은 경우, 인스턴스 2개가 생성되는 문제 발생하거나 데이터가 잘못될 경우가 존재한다.

### 멀티스레드 환경에서 안전한 싱글톤 만드는 방법
### 1. Thread Safe Lazy Initialization (Thread safe 게으른 초기화)
* Lazy Initialization + `synchronized` 키워드
* 장점
  * Lazy initialization 방식에서 thread-safe하지 않은 점을 보완
* 단점
  * `synchronized` 키워드를 사용할 경우 자바 내부적으로 해당 영역이나 메서드를 lock, unlock 처리하기 때문에 
  내부적으로 많은 cost가 발생한다. 👉 성능저하


``` java
  public class ThreadSafeLazyInitialization {

      private static ThreadSafeLazyInitialization instance;
  
      private ThreadSafeLazyInitialization(){}
      
      // synchronized 키워드!
      public static synchronized ThreadSafeLazyInitialization getInstance(){
          if(instance == null){
              instance = new ThreadSafeLazyInitialization();
          }
          return instance;
  }

}
  ```
### 2. Thread safe Lazy initialization + Double-checked locking 기법
* Thread Shafe Lazy Initialization 성능저하 보완 가능
``` java
public class ThreadSafeLazyInitialization {

	private static ThreadSafeLazyInitialization instance;

	private ThreadSafeLazyInitialization(){}
	
	public static ThreadSafeLazyInitialization getInstance(){
		//Double-checked locking
		if(instance == null){
		    // instance 가 null인 경우 synchronized 블럭에 접근
			synchronized (ThreadSafeLazyInitialization.class) {
			    // instance null 체크
				if(instance == null)
					instance = new ThreadSafeLazyInitialization();
			}

		}
		return instance;
	}
}
```
### 3. Initialization on demand holder idiom (holder에 의한 초기화)
* 클래스안에 클래스(Holder)를 두어 JVM의 Class Loader 매커니즘과 Class가 로드되는 시점을 이용한 방법
* 현재까지 가장 많이 사용되는 방법
``` java
public class InitializationOnDemandHolderIdiom {

	private InitializationOnDemandHolderIdiom(){}
	
	// 중첩클래스 Holder는 getInstance 메서드가 호출되기 전에는 참조 되지 않는다.
	private static class SingleTonHolder{
	    // static이기 때문에 클래스 로딩 시점에 한번만 호출된다.
	    // final을 써서 다시 값이 할당되지 않도록 한다.
		private static final InitializationOnDemandHolderIdiom instance = new InitializationOnDemandHolderIdiom();
	}
	
	// 최초로 getInstance() 메서드가 호출 될 때 클래스 로더에 의해 싱글톤 객체를 생성하여 리턴한다.
	public static InitializationOnDemandHolderIdiom getInstance(){
		return SingleTonHolder.instance;
	}
}
```


### 싱글톤, 언제 자주 사용되나요? 😶
* 주로 공통된 객체를 여러 개 생성해서 사용할 때  
  * DB Connection Pool, 스레드 풀, 캐시, 로그 기록 객체 등
* 인스턴스가 절대적으로 한 개만 존재하는 것을 보증하고 싶을 때 사용

<br/><br/><br/>

### 안티패턴 싱글톤.. 💭
#### 주절주절 🤓

일반적으로 `new`로 생성한 평범한 변수는 그 요청에만 국한되고, 싱글톤 같은 `static` 변수는 모든 요청이 공유한다.  


프로그램 자체는 여러 개 실행될 수 있다.   
컴퓨터를 여러 대 쓰는 경우, A 프로그램을 여러 컴퓨터에서 돌리려면 당연히 각 컴퓨터마다 그 프로그램을 한 번씩 켜야 한다.  
그럼 같은 프로그램이 여러 번 뜨게 되는 데, 여기서 띄워진 프로그램 하나하나를 프로세스라고 한다.  
이 경우에도 `new`로 생성한 평범한 변수는 그 요청에만 국한되지만 **`static` 변수는 프로세스의 개수만큼 생기게 된다.**  
**모든 요청이 공유해야 하는 정보는 일반적으로 `static` 변수에 담지 않는다.**  
보통 **데이터베이스** 같은 외부 구조를 사용한다. *사용자 비밀번호, 게시판, 주문정보 등 웹 서비스가 처리해야 하는 거의 모든 정보*가 그렇다.  

<br/>

왜 다들 싱글톤을 지양하는지, 강의에서만 보게 되었는지.. 다시 한 번 되돌아보는 계기가 되었다. 🙂


[참고](https://limkydev.tistory.com/67)