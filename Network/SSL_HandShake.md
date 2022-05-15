# SSL HandShake
## HTTP
### 정의
- HyperText Transfer Protocol
- 웹 브라우저와 웹서버, 애플리케이션 및 IoT 등과 데이터를 주고 받기 위한 프로토콜

### 특징
- Stateless
	- 클라이언트의 상태를 저장하지 않음 -> 때문에 클라이언트가 이전에 했던 요청에 따라 반응이 달라지지 않는다!
		- 여러 대의 서버가 어떤 요청에 따라 동일한 응답을 하기 때문에 서버 확장이 가능!
- Connectionless
	- 연결을 유지하지 않음
	- 연결을 유지할 경우 지속적인 리소스가 사용됨!
- 클라이언트 - 서버 구조
	- 클라이언트의 요청이 있을 때 서버가 응답하는 단반향 통신
- 대부분의 파일 형식 전송 가능
	- 거의 모든 파일 형식을 HTTP 통신을 이용해 전송 가능

## HTTPS
### 정의
- 안전한 통신을 위해 HTTP 통신에 암호화를 적용
- SSL(Secure Socket Layer) / TLS(Transport Layer Security) 전송 기술 사용
- TCP, UDP와 같은 일반적인 인터넷 통신에 **안전한 계층(Layer)**를 추가

### 대칭키 암호화 방식
![대칭키 암호화](https://github.com/leeejuhyeong/images/blob/main/no-study-no-future/Network/%E1%84%83%E1%85%A2%E1%84%8E%E1%85%B5%E1%86%BC%E1%84%8F%E1%85%B5%20%E1%84%8B%E1%85%A1%E1%86%B7%E1%84%92%E1%85%A9%E1%84%92%E1%85%AA.png?raw=true)   

### 공개키 암호화 방식(비대칭키 암호화 방식)
![비대칭키 암호화](https://github.com/leeejuhyeong/images/blob/main/no-study-no-future/Network/%E1%84%87%E1%85%B5%E1%84%83%E1%85%A2%E1%84%8E%E1%85%B5%E1%86%BC%E1%84%8F%E1%85%B5%20%E1%84%8B%E1%85%A1%E1%86%B7%E1%84%92%E1%85%A9%E1%84%92%E1%85%AA.png?raw=true)   


### 순서
1. TCP, UDP 연결
2. SSL HandShake
3. 데이터 통신

### SSL HandShake
1. 클라이언트 - 클라이언트가 먼저 서버에 접속(Client Hello)
2. 웹서버 - 웹서버 인사 응답, 클라이언트에 정보 전달(Server Hello)
3. 클라이언트 - 웹서버의 인증서 확인, 웹서버의 공개키 획득
4. 웹서버의 공개키로 Premaster Secret을 암호화해서 전달(Premaster Secret : 브라우저와 웹서버의 난수로 생성된 일련의 값)
5. 웹서버의 개인키로 Premaster Secret 복호화
	- 클라이언트, 웹서버 모두 일련의 과정을 거쳐서 Premaster Secret으로 Master Secret을 생성
	- Master Secret으로 세션키 생성
6. SSL handshake 종료 및 HTTPS 시작
![SSL Handshake](https://github.com/leeejuhyeong/images/blob/main/no-study-no-future/Network/SSL%20HandShake.png?raw=true)   

### HTTPS를 적용하면 완벽 보안?
- HTTPS는 웹에서 보안을 적용하기 위한 가장 기본적인 단계
- HTTPS는 전달 구간에 대한 보안, 중간 서버를 해커가 침투하면 내용은 고스란히 유출
- HTTPS를 적용하면서 종단간 암호화 기술(E2EE : End-to-End Encryption) 적용하는 것이 일반적인 추세
> E2EE?  
>  - 일반적으로 E2EE가 적용되지 않는다면 발신원과 수신원 사이 중간서버에서 데이터는 암호화되지 않은 채로 저장되거나 잔류됨  
>  - E2EE를 적용할시 발신원의 정보가 **암호화된 채 그대로** 중간 서버를 거쳐 수신원에게 전달  

## 출처
- [HTTPS를 위한 SSL/TLS 핸드 셰이크 작동원리](https://brunch.co.kr/@sangjinkang/38)
- [(CS) TLS/SSL Handshake란](https://hanjungv.github.io/2017-11-07-1_CS_SSL/)
- https://hanamon.kr/%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC-http-http%EB%9E%80-%ED%8A%B9%EC%A7%95-%EB%AC%B4%EC%83%81%ED%83%9C-%EB%B9%84%EC%97%B0%EA%B2%B0%EC%84%B1/
- [Network HTTP란 무엇인가?](https://surprisecomputer.tistory.com/54)
- [SSL이란 무엇인가 :: 매일의 공부 기록](https://study-recording.tistory.com/11)