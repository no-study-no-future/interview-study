# REST API
REST API : REST 아키텍처의 제약조건을 준수하는 API (Application Programming Interface)   
종종 REST같은 API라고 RESTful API라고도 한다.

## REST (REpresentational State Transfer)
WWW와 같은 분산 하이퍼미디어 시스템을 위한 소프트웨어 아키텍처 스타일 (제약조건들의 집합)인데, 분산 하이퍼미티어 시스템 내의 아키텍처를 추상화 한 것이다.

REST는 네트워크 어플리케이션의 필수 동작과 웹 아키텍처를 기초를 정의하는 Components, connectors, data을 포함하고 있다. 하지만 구성요소의 역할, 다른 구성요소와 상호 작용에 대한 제약조건 및 중요한 데이터 요소의 해석에 초점을 맞추기 위해 구성 요소 구현 및 프로토콜 구문의 세부사항은 무시한다. 

---
### 데이터 요소 (Data element)
|Data Element|Modern Web Examples|
|:--:|:--|
|resource	|the intended conceptual target of a hypertext reference
|resource identifier	|URL, URN
|representation	|HTML document, JPEG image
|representation metadata|	media type, last-modified time
|resource metadata	|source link, alternates, vary
|control data	|if-modified-since, cache-control|

REST에서 정보의 핵심 추상화는 Resource(자원)이다. 문서, 이미지, 일회성 서비스 (오늘의 날씨), 자원들의 집합, 가상이 아닌 객체 등을 모두 `Resource(자원)`이라고 통칭한다.

---
### 제약조건
#### **1. Client-Server**
**관심사의 분리**가 핵심 원칙. 사용자 인터페이스 문제를 데이터, 뷰와 분리해 이식성(portability)과 확장성(scalability)을 개선한다. [추가정보](https://www.ics.uci.edu/~fielding/pubs/dissertation/net_arch_styles.htm#sec_3_4_1)
#### **2. Stateless**
Client-Server 구조에 있어서 서버는 Client의 정보를 저장하면 안된다. 그렇기 때문에 Client는 요청을 보낼 때 마다 필요한 정보를 모두 포함해야한다.   
이 제약조건을 통해 가시성(Visibility), 안정성(reliability) 및 확장성(scalability)을 유도할 수 있다.
- Visibility : 모니터링 시스템을 개선 -> 모니터링 시스템이 요청온 데이터 이상을 확인할 필요가 없음.
- Reliability : 부분적 실패를 복구하는 작업을 용이하게 해줌 *
- Scalability : 상태를 저장할 필요가 없기 때문에 서버의 리소스 공간 확보, 사용량 관리 비용 절감, 구현의 단순성 확보   
하지만 `Stateless`의 단점으로 매 요청마다 필요한 정보를 모두 포함시켜야하므로 네트워크의 성능을 저하시킬 수 있으며, 클라이언트에 대한 서버의 주도권이 줄어듭니다.

#### **3. Cache**
네트워크 효율성을 향상시키기 위해 [Client-Cache-Stateless-Server](https://www.ics.uci.edu/~fielding/pubs/dissertation/net_arch_styles.htm#sec_3_4_4)의 `Cache 제약조건`을 적용. 이 조건을 요청의 응답이 Cacheable 한지 유무를 명시적 혹은 암시적으로 알려줘야한다(Label).   

요청의 응답을 캐시할 수 있는 경우 클라이언트의 반복된 요청에 대해 클라이언트가 캐시에 접근할 수 있는 권한을 얻는다.

`Cache 제약조건`을 사용하면 일부 요쳥에 대한 비용을 줄여 사용자 입장에서의 성능 향상이 이루어지지만 캐시의 데이터가 오래된 데이터일 경우 문제가 될 수 있다. 

#### **4. Uniform Interface**
REST 아키텍처 스타일을 다른 네트워크 아키텍처와 구분하는 중요한 원칙이다. 컴포넌트 간 통일된 인터페이스를 사용한다.  

이 원칙을 통해 전체적인 아키텍처가 단순화 되고 인터페이스와 서비스가 분리시킬 수 있다. 하지만 반대로 정해진 응답을 보내야하기 때문에 필요한 데이터만을 포함하지 못해 효율이 떨어지는 단점이 있다.

대규모 하이퍼미디어 데이터 전송에 효율적이게 설계되어있어 웹이 아닌 형태의 아키텍처 상호작용에는 최적화가 되어있지 않다. [Uniform interface 제약조건](#Uniform-interface의-제약조건)

#### **5. Layered System**
`Layered System`를 사용하면 각 구성요소가 상호작용 하는 계층을 제외하고는 알 수 없다. 이를 통해 시스템의 복잡성을 줄이고 확장성을 높일 수 있다.

하지만 계층형 구조의 단점은 계층간 데이터 전송때문에 발생하는 오버헤드이다. 3의 `Cache 제약조건`을 지원하는 경우 캐싱의 이점으로 이를 상쇄시킬 수 있다.

#### **6. Code-On-Demand (Option)**
서버가 클라이언트에게 실행시킬 수 있는 코드를 전송하여 기능을 확장시킬 수 있다.

---
### Interface 제약조건
#### **identification of resources(리소스의 식별)**

#### **manipulation of resources through representations(메세지를 통한 리소스 조작)**

#### **self-descriptive messages(메세지 자기소개)**

#### **hypermedia as the engine of application state(엔진으로써의 하이퍼미디어)**

---
### 참고

[Red Hat - What is a rest api](https://www.redhat.com/ko/topics/api/what-is-a-rest-api)   
Stateless-Reliability : J. Waldo, G. Wyant, A. Wollrath, and S. Kendall. A note on distributed computing. Technical Report SMLI TR-94-29, Sun Microsystems Laboratories, Inc., Nov. 1994.

---
최초 작성일 : 2022.04.24.   
최초 작성자 : [Kodo92](https://github.com/Kodo92)

---