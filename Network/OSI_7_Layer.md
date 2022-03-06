# OSI 7 Layer (OSI 7 계층)

국제표준기구 ISO가 발표한 네트워크 모델 

|    OSI 7 Layer    |
|:-----------------:|
| APPLICATION LAYER |
| PRESENTATION LAYER|
|   SESSION LAYER   |
|  TRANSPORT LAYER  |
|   NETWORK LAYER   |
|  DATA LINK LAYER  |
|   PHYSICAL LAYER  |


### 왜 여러 계층으로 나누었을까? 🤔
- 통신이 일어나는 과정을 단계별로 파악할 수 있다.
- 특정한 곳에 이상이 생긴다면 다른 단계의 장비 또는 소프트웨어를 건드리지 않고 이상이 생긴 단계만 고칠 수 있다.




## 7계층 APPLICATION LAYER (응용 계층)
- 응용 프로세스를 직접 사용하여 직접적인 응용 서비스를 수행하는 계층


- 프로토콜 데이터 단위(PDU, Protocol Data Unit): Data
- 프로토콜(Protocol): DHCP, DNS, FTP, HTTP



## 6계층 PRESENTATION LAYER (표현 계층)
- 데이터의 변환 / 압축 / 암호화가 이루어지는 계층
- 서로 다른 통신 기기 간 다른 인코딩을 사용할 수 있기 때문에 해당 계층에서 데이터 변환이 이루어진다.


- 프로토콜 데이터 단위(PDU, Protocol Data Unit): Data
- 프로토콜(Protocol): JPG, MPEG, SMB, AFP




## 5계층 SESSION LAYER (세션 계층)
- 세션을 열고 닫고를 제공하는 메커니즘의 계층
- 세션 복구 지원
  - 체크포인트를 통해 동기화를 시켜준다.
  - ex) 체크포인트가 5MB이고 컴퓨터A 👉 컴퓨터B 데이터 100MB를 전송하다가 48MB의 데이터를 전송하는 도중에 
        연결이 끊기게 되면 체크포인트 덕분에 다시 45MB부터 세션을 재개할 수 있다.


- 프로토콜 데이터 단위(PDU, Protocol Data Unit): Data
- 프로토콜(Protocol): NetBIOS, SSH, TLS




## 4계층 TRANSPORT LAYER (전송 계층)
- 서로 다른 두 네트워크 간의 전송을 담당하는 계층
- 세그멘테이션, 흐름제어, 오류제어 등 제공
- 세그멘테이션
  - 상위 계층 데이터를 받아서 세그먼트라는 단위로 나누는 것
  - 세그멘테이션 작업을 하는 이유
    - ex) 한 컴퓨터에서 100MB 비디오를 전송할 때, 세그멘테이션을 하지 않는 다면 100MB가 다 로딩되고 나서야 비디오를 볼 수 있다.
          세그먼트 작은 단위로 나누게 되면 비디오 일부분을 볼 수 있게 된다.  
          또한 세그멘테이션 하지 않을 때 연결이 중간에 끊기게 되는 경우, 큰 데이터가 날아가 손실율이 더 크다.
- 흐름제어
  - 서로 다른 데이터전송량이 다른 기기에서 전송량을 낮추거나 높여달라고 요청할 수 있다.
  - ex) `Stop and Wait`, `Sliding Window`
- 오류제어
  - 보낸 데이터가 정확히 오류 손실이 없는지, 만약에 오류가 있으면 해당 데이터를 다시 보내주는 역할
  - ex) `FEC`, `BEC`, `ARQ`


- 프로토콜 데이터 단위(PDU, Protocol Data Unit): Segment
- 프로토콜(Protocol): TCP, UDP, ARP, RTP
- 대표 장비: 게이트웨이, L4 스위치




## 3계층 NETWORK LAYER (네트워크 계층)
- **데이터 전송 담당**하는 계층
- IP(주소부여)나 라우터(경로 설정) 장비가 속해 있다.
- 호스트에 IP 번호를 부여하고 해당 도착지 IP까지 최적의 경로를 찾아 주는 기능`(라우팅)`을 제공한다.


- 프로토콜 데이터 단위(PDU, Protocol Data Unit): Packet
- 프로토콜(Protocol): IP, ICMP 등
- 대표 장비: 라우터, L3 스위치




## 2계층 DATA LINK LAYER (데이터 링크 계층)
- 네트워크 계층과 상당히 비슷
- 네트워크 계층은 서로 다른 두 네트워크 간의 전송을 담당한다면 데이터 링크 계층은 **동일한 네트워크 내에서 전송을 담당**하는 계층
- 오류제어와 흐름제어 제공
- 데이터 링크 계층의 데이터 단위 `Frame`
  - 데이터 링크 계층에서는 Frame에서 오류가 났을 때 이 데이터 조각들을 그냥 버려 버린다.
  - 그에 반면하여 트랜스포트 계층은 해당 데이터 없으면 다시 보내주어 오류 복구까지 해준다.


- 프로토콜 데이터 단위(PDU, Protocol Data Unit): Frame
- 프로토콜(Protocol): 이더넷, MAC, PPP, ATM, LAN, Wifi
- 대표 장비: 브릿지, 스위치




## 1계층 PHYSICAL LAYER (물리 계층)
- bit 단위 데이터(ex. 010101000101111)를 전기 신호로 변환해주고 전송을 해주는 역할


- 프로토콜 데이터 단위(PDU, Protocol Data Unit): Bit
- 프로토콜(Protocol): Modem, Cable, Fiber, RS-232C
- 대표 장비: 허브, 리피터



<br/><br/>

### 현재 우리가 사용하고 있는 네트워크 모델은 OSI 모델이 아닌 TCP/IP 모델을 사용하고 있다.
- OSI 모델은 단지 네트워크 묘사해주기 위한 모델이다.
- TCP/IP 모델은 5계층인 SESSION 계층과 6계층인 PRESENTATION 계층이 APPLICATION 계층으로 통합되어 있다.
- [왜 TCP/IP에서는 OSI의 5,6,7계층이 하나의 계층에 들어가는가?](https://engineeringcode.tistory.com/58)

|    TCP/IP MODEL   |
|:-----------------:|
| APPLICATION LAYER |
|  TRANSPORT LAYER  |
|   NETWORK LAYER   |
|  DATA LINK LAYER  |
|   PHYSICAL LAYER  |


(TCP IP Model 기준)  
ex)   
A에서 B로 데이터를 보낼 때, 상위계층(application layer)에서 하위계층(physical layer)으로 데이터를 내려 받으면서  
계층별 헤더를 붙이고 B로 보내고 B에서는 해당 캡슐화된 데이터들을 다시 디캡슐레이션을 하면서 데이터를 얻는 방식이다.  


1. Application Layer  
- 우리는 보통 Http 프로토콜을 이용해서 데이터를 보내게 된다.

2. Transport Layer
- TCP를 사용할 건지 UDP를 사용할 건지 정해야 한다.  
  - TCP: 보낸 데이터를 손실되었는지 확인하고 데이터의 순서도 보장하기 때문에 조금 더 신뢰적이다.
  - UDP: 일단 데이터를 보내고나면 그에 대한 책임을 지지않는다. 그만큼 속도 빠르고 연속적이라 스트리밍 서비스에서 많이 사용된다.
- 헤더(L4 header)에 TCP/UDP 정보와 출발지/도착지 포트를 추가하여 데이터 뒤에 붙인 뒤 캡슐화하여 하위 계층인 Network Layer에 보낸다.
- Segment: Data + L4 Header (TCP or UDP + 출발지 포트 + 도착지 포트 + ..) 캡슐화

3. Network Layer
- 헤더(L3 header)에 출발지/도착지 IP 정보를 추가하여 세그먼트 뒤에 붙인 뒤 캡슐화하여 하위 계층인 Data Link Layer에 보낸다.
- Packet: Segment(Data + L4 Header) + L3 Header (출발지와 도착지에 대한 IP 정보 + ..) 캡슐화

4. Data Link Layer
- A(출발지) 맥 주소와 가장 가까운 라우터의 맥 주소를 넣는다.
  - B(도착지)의 맥 주소를 넣지 않는 이유: A는 B의 맥 주소를 모르기 때문
- DHCP와 ARP를 통해서 라우터 IP를 맥주소로 변환한 후에 라우터에 대한 도착지 맥 주소를 만든 후 헤더(L2 Header)에 넣어준다.
- 오류제어를 위한 정보가 있는 L2 Trailer라는 정보도 붙는다.
- 이 데이터를 캡슐화하여 Physical Layer에 보낸다.
- Frame: L2 Trailer (오류제어 정보) + Packet (Data + L4 Header + L3 Header) + L2 Header (출발지 맥 주소 + 도착지(가장 가까운 라우터) 맥 주소 + ..) 캡슐화

5. Physical Layer
- 전기신호로 바꾸어 데이터를 전송한다.




![TCP/IP MODEL](https://github.com/ChaerinYu/Today-I-Learned/blob/main/Network/images/tcpipmodel.png?raw=true)  


1. A에서 먼저 스위치로 데이터를 보낸다. 
2. 스위치는 디캡슐레이션을 한 번 진행하여 헤더에 대한 정보를 살펴본다. 라우터에 대한 맥 주소를 확인하여 해당 라우터에 데이터를 보내준다.  
   (스위치는 데이터링크계층의 대표적인 하드웨어로 캠 테이블이 있어 스위치와 연결되어있는 맥 주소에 대한 정보를 갖고 있다.  
   해당 주소에 포함되는 맥 주소의 데이터가 오면 해당 기기로 보내준다.)
3. 라우터(네트워크 계층의 하드웨어)에서는 디캡슐레이션을 두 번해서 도착지에 대한 IP 주소를 확인한 후 
   Layer 2에 대한 헤더 정보(출발지 맥 주소와 도착지 맥 주소)를 업데이트 하고 라우팅 테이블을 통해 라우팅한다.  
   (실 네트워크에서는 라우터 수 만큼 Layer 2에 있는 출발지/도착지 맥 주소를 계속 업데이트 한다.)
4. 라우터에서 스위치로 보내고 스위치는 헤더 정보를 확인한 후, 캠 테이블을 통해 B에게 보낸다.


<br/><br/>
<br/><br/>

[참고1](https://snyung.com/content/2020-08-31--%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC-%EA%B8%B0%EC%B4%88-osi-7-%EA%B3%84%EC%B8%B5%EA%B3%BC-tcp-ip-%EA%B3%84%EC%B8%B5/)    
[참고2](https://www.youtube.com/watch?v=Fl_PSiIwtEo)


