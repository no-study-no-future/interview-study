# Exception 종류와 처리 방법

## 요약
- Checked Exception은 컴파일 오류로 실행 시점에서 발생하며, Unchecked Exception은 런타임 오류로 개발자의 실수로 발생하는 경우가 많다
- Checked Exception은 오류 시 트랜잭션 롤백이 발생하지 않고 Unchecked Exception은 롤백이 발생한다

## Exception 종류
![Exception 종류](https://github.com/leeejuhyeong/images/blob/main/no-study-no-future/Java/Exception%20%E1%84%8C%E1%85%A9%E1%86%BC%E1%84%85%E1%85%B2.jpeg?raw=true)
  
- Error
- Exception
	- Checked Exception
	- Unchecked Exception
  
  
## Error
- java.lang.Error 클래스의 서브 클래스
- JVM에서 발생하며 메모리 부족 등과 같은 **시스템이 비정상적인 상황**인 경우에 사용
- 애플리케이션 코드에서 잡아서는 안되며, 잡아서 대응할 수 있는 방법도 없음.
  
  
## Exception
- java.lang.Exception 클래스의 서브 클래스
- 애플리케이션 코드에서 예외가 발생했을 경우 사용
- Checked Exception, Unchecked Exception가 있다
  
### Checked Exception
- RuntimeException 클래스를 상속하지 않은 Exception 클래스
- 예외를 처리하지 않으면 컴파일 에러가 발생
- 따라서 예외를 처리하기 위해 try-catch 또는 throws를 통해 처리
> FileNotFoundException, ClassNotFoundException, IOException, SqlException 등  
  
### Unchecked Exception
- RuntimeException 클래스를 상속한 Exception 클래스
- 예외를 처리하지 않아도 컴파일 에러가 발생 ❌
- 발생하는 예외는 RuntimeException을 상속했기에 동일
  
### Runtime Exception이란
- 실행 중에 발생하는 예외로, 시스템 환경적으로나 input값이 잘못된 경우, 혹은 의도적으로 프로그래머가 잡아내기 위한 조건 등에 부합될 때 발생
> ArrayIndexOutofBoundsException, NullPointerException, IllegalArgumentException 등  
  
### 구분 기준(차이)
- 예외를 확인할 수 있는 시점 구분
	- Checked Exception은 컴파일 단계에서 명확하게 체크가 가능한 것들, 실행 ❌
	- Unchecked Exception은 실행 과정 중 어떠한 특정 논리에 의해 발견
  
### 왜 알아야할까?
- 꼭 처리를 해야하는 기준이 된다
	- Checked Exception의 경우 예외를 처리하지 않으면 컴파일 에러가 발생하기 때문에 반드시 예외 처리가 필요
	- Unchecked Exception은 개발자의 부주의로 발생하는 경우가 대부분이며, 미리 예측하지 못했던 상황에서 발생하는 예외가 아니기 때문에 굳이 로직으로 처리할 필요가 없도록 만들어져 있다
- 예외 발생 시 트랜잭션의 Roll-Back 여부
	- Checked Exception의 경우 예외 발생 시  트랜잭션이 Roll-Back하지 않는다
	- Unchecked Exception의 경우 예외 발생 시 트랜잭션이 Roll-Back한다
	- Spring Framework가 제공하는 선언적 트랜잭션(@Transactional)안에서 에러 발생 시 Checked Exception은 롤백이 되지 않고, Unchecked Exception은 롤백이 됨(물론 옵션을 변경할 수 있음)
  
  
## 예외 처리 방법
### 예외 복구
- 문제를 해결해서 정상 상태로 돌려놓는 것
  
### 예외 처리 회피
- 예외 처리를 직접 처리하지 않고, 자신을 호출한 곳으로 던져버리는 것
- 무작정 예외를 넘기는 것은 무책임한 회피가 될 수 있으므로 상황에 따라 적절한 사용이 필요
  
### 예외 전환
- 목적
	- 의미 있고 추상화된 예외로 바꾸는 경우
		- 중복 아이디 체크 : 동일한 아이디가 존재하면 SqlException이 발생하지만 이를 DuplicatedUserIdException과 같은 예외로 변환하면서 서비스 계층에서 왜 예외가 발생했는지 확실한 의미를 전달
	- 런타임 예외로 포장하여 불필요한 처리를 줄요주는 경우
		- Checked Exception으로 의해 불필요한 에러 처리가 많다면 이를 Unchecked Exception으로 변경하여 불필요한 처리를 줄임
		- 복구 못할 예외라면 불필요하게 체크를 할 필요가 없기 때문
  
  
## 올바른 예외 처리 방법
- 조치가 없는 Try-Catch → 상황에 맞는 조치 진행
	- Try-Catch로 예외를 잡을 경우 반드시 조치를 해주자
- 무분별한 throws Exception → Unchecked Exception으로 전환
	- 무책임한 throws Exception은 어떤 문제가 발생했는지(의미 있는 정보)를 얻을 수 없기 때문
	- SqlException과 같은 복구가 불가능한 예외라면 가능한 빨리 Unchecked/Runtime 예외로 전환해주는 것이 좋음
  
  
## 출처
- [Java 예외(Exception) 종류와 올바른 예외 처리 방법 - MangKyu’s Diary](https://mangkyu.tistory.com/152)
- [Java Checked Exception vs Unchecked Exception 정리 :: Gyun’s 개발일지](https://devlog-wjdrbs96.tistory.com/351)
- [RuntimeException란 무엇인가?](https://nhj12311.tistory.com/204)
- [Java 예외(Exception) 처리에 대한 작은 생각](https://www.nextree.co.kr/p3239/)