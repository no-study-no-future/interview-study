
# 가비지 컬렉션 (Garbage Collection)

### ✔ 요약
> 더 이상 사용하지 않는 메모리를 **자동으로 정리**한다.

<br/><br/>

* JVM 구성요소

![JVM구성요소](https://github.com/ChaerinYu/Today-I-Learned/blob/main/Java/images/jvm2.png?raw=true)

## 정의
* JVM의 Heap 영역에서 사용하지 않는 객체를 삭제하는 프로세스
* heap영역에는 object 타입의 데이터들이 들어간다. String, List, 또는 커스텀 클래스들이 포함된다.

<br/>

## GC 대상?
* 어떤 객체에 유효한 참조가 존재한다면 reachable, 그렇지 않다면 unreachable 이라고 한다.

![GC_대상](https://github.com/ChaerinYu/Today-I-Learned/blob/main/Java/images/gc_object.png?raw=true)

* GC Roots 될 수 있는 데이터들
    * stack 영역의 데이터들
        * 지역변수, 파라미터
    * method 영역의 static 데이터들
    * JNI에 의해 생성된 객체들
* 'Stack에 있는 객체에 참조'를 받거나 'method 영역에서 참조를 받는 객체'나 또는 'heap 영역 다른 객체에게 참조' 받는 경우 reachable 하다고 한다.
* unreachable이 GC 수거 대상이다

<br/>

## GC의 동작 알고리즘
### Mark and Sweep
* 기본적으로 이 알고리즘으로 작동한다.
* mark
    * GC Root로 부터 모든 변수를 스캔하면서 각각 어떤 객체를 참조하고 있는지 찾아서 마킹한다.
        * reachable 객체에 marking
    * reachable과 unreachable을 식별하는 과정
* sweep
    * unreachable 객체들을 heap에서 제거한다.
* compact
    * 알고리즘에 따라서 compact 과정이 추가되기도 한다.
    * sweep 후에 분산된 객체들을 한 곳(heap의 시작 주소)에 모아서 메모리 단편화를 막아주는 작업이다.
    * 메모리가 할당된 부분과 그렇지 않은 부분으로 나눈다.
* GC가 한 번 발생할 때마다 mark, sweep 어쩔 때는 compact까지 한 사이클을 돈다.

> #### 메모리 단편화 🤔💭
> - RAM에서 메모리의 공간이 작은 조각으로 나뉘어져 **사용가능한 메모리가 충분히 존재하지만 할당(사용)이 불가능한 상태**


<br/><br/>

## GC는 언제 발생할까?
### Heap의 구조 (java 8 기준)
* Young generation과 Old generation으로 구분이 된다.
* Young generation
    * 새로운 객체들이 할당되는 영역
    * `Eden`, `Survivor 0`, `Survivor 1`로 나뉜다.
* Old generation
    * Young generation에서 오래동안 살아남은 객체들이 존재하는 영역

![Heap](https://github.com/ChaerinYu/Today-I-Learned/blob/main/Java/images/javaheap.png?raw=true)  

> #### Perm
> * Perm 영역은 보통 Class Meta 정보나 Method의 메타 정보   
static 변수와 상수 정보들이 저장되는 공간으로 흔히 메타데이터 저장 영역이라고 한다.  
이 영역은 JAVA8 부터 Native Memory 영역으로 이동하였다. (기존의 Perm영역에 존재하는 static object는 Heap 영역으로 옮겨졌다.)  
> #### Metaspace
>  * garbage collection에서 필요한 클래스와 메소드의 요약 정보(metadata)가 존재하는 영역  
>
>
> Native Memory 영역은 일반적으로 OS Level에서 관리되며 Permanent 영역에 저장 되었던 Class나 Method의 메타 정보들이
> Metaspace 영역으로 변경됨에 따라 Heap 영역의 확보의 Max 값을 크게 의식하지 않아도 된다.    
> Perm영역과 Metaspace 영역의 기본 값은 시스템 별로 크게 다를 수 있으므로 튜닝 시 초기치와 최대치 확인 필요하다.
>

<br/>

### GC의 과정  


![GC_과정](https://github.com/ChaerinYu/Today-I-Learned/blob/main/Java/images/howtogc.png?raw=true)  


1. 새로운 객체가 `Eden` 영역에 할당된다.
2. `Eden`영역에서 할당될 공간이 없으면 `Minor GC` 발생한다. (Mark And Sweep)
    * GC에서 살아남은(`reachable`) 객체들은 `Survivor` 영역으로 이동한다.
    * `Survivor` 영역에는 규칙이 존재한다.
    * `Survivor 0`에 객체가 있는 경우 `Survivor 1`은 비워져 있어야 하며 `Survivor 1`에 객체가 있는 경우에는 `Survivor 0`이 비워져 있어야 한다.
    * 따라서 `Survivor 0`에 객체를 보내든 `Survivor 1`에 객체를 보내든 상관 없다.
    * Sweep 과정을 통해 `Unreachable` 객체들이 사라진다. 👉 Sweep
    * Survivor영역에 있는 객체들의 age가 증가한다. 👉 `Aging`
3. 다시 `Eden` 영역이 꽉차게 되면 `Minor GC` 발생한다.
4. 살아남은 객체들은 '2' 에서 비어있는 `Survivor` 영역으로 이동한다. (Mark and Sweep)
5. 계속 반복되다 보면 객체의 age가 age threshold(임계점)에 도달하면 `Old Generation`으로 이동한다. 👉 `Promoted`
6. age 값이 높은 객체들로 `Old Generation`이 꽉차게 되면 `Major GC`가 발생한다.


<br/>

### 왜 Minor GC와 Major GC로 나누었을까? 🤔
* GC 설계자들의 가설
1. 대부분의 객체는 금방 `unreachable` 상태가 된다. 👉 즉, 금방 garbage가 된다.
2. Old 객체에서 young 객체로의 참조는 아주 적게 존재한다.


![object](https://github.com/ChaerinYu/Today-I-Learned/blob/main/Java/images/gc_object_allocation.png?raw=true)  
[사진출처](https://docs.oracle.com/javase/8/docs/technotes/guides/vm/gctuning/generations.html)

* Minor GC가 일어난 후 꽤 많은 객체들이 수거되는 모습을 볼 수 있다.
    * 빈번하게 일어나도 메모리 낭비를 막는 장점이 더 큼
* Major GC는 일어난 후 수거되는 객체가 적다.

## `stop-the-world`
* GC를 실행하기 위해 JVM이 애플리케이션 실행을 멈추는 행동
    * GC를 실행하는 스레드 외 모든 스레드가 작업을 중단한다.
* GC종류에 따라 stop-the-world 발생하는 시간이 다르다.

## GC 종류
### Serial GC
* 가장 단순하고 기본적인 GC
* GC를 처리하는 스레드가 1개 👉 싱글 스레드
* 다른 GC에 비해 stop-the-world 시간이 길다.
* Mark-Sweep-Compact 알고리즘 사용
### Parallel GC
* JAVA 8의 기본 GC
* Young Generation에서 멀티 스레드로 GC 수행
* Serial GC에 비해 stop-the-world 시간 짧다.
### Parallel Old GC
* Parallel GC를 개선
* Old Generation에서도 멀티 스레드로 GC 수행
* Mark-Summary-Compact 알고리즘 사용
    * sweep: 싱글 스레드가 old generation 전체를 훑는다.
    * summary: 멀티 스레드가 old generation 분리해서 훑는다.


![gc1](https://github.com/ChaerinYu/Today-I-Learned/blob/main/Java/images/gc_cate1.png?raw=true)

### CMS (Concurrent Mark Sweep) GC
* stop-the-world 로 자바 어플리케이션이 멈추는 현상을 줄이고자 만들어진 GC
* compact 과정이 없어 메모리단편화 문제 발생 가능
    * `initial mark`: GC Root에서 참조하는 객체들만 우선 식별 (stop-the-world 발생)
    * `concurrent mark`: 이전 단계에서 식별한 객체들이 참조하는 모든 객체 추적 (stop-the-world 발생 안함)
    * `remark`: 이전 단계에서 식별한 객체를 다시 추적하여 추가되거나 참조가 끊긴 객체 확정 (stop-the-world 발생)
    * `concurrent sweep`: 최종적으로 unreachable 객체 삭제 (stop-the-world 발생 안함)


![gc2](https://github.com/ChaerinYu/Today-I-Learned/blob/main/Java/images/gc_cate2.png?raw=true)

### G1 (Garbage First) GC
* CMS GC를 개선 (메모리 단편화 문제 개선)
* 현재 GC 중 stop-the-world 시간이 제일 짧다.
* Java 9이상에서의 기본 GC
* Heap을 `Young Generation`, `Old Generation`으로 물리적으로 나누지 않고 **일정한 크기의 `region`**으로 나눔
    * 가비지만 있는 region은 처음에 수거해가기 때문에 Garbage First라는 이름을 갖게 됨
* marking할 때 전체 heap을 탐색하지않고 할당된 region에 대해서만 찾는 방식으로 동작



<br/><br/>

[참고1](https://www.youtube.com/watch?v=Fe3TVCEJhzo)  
[참고2](https://swiftymind.tistory.com/112)  
[참고3](https://memostack.tistory.com/229)  
