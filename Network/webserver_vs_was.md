
# Web Server
* **웹 브라우저 (클라이언트)** 로 부터 **http 요청** 을 받아 **html 문서**와 같은 ~~정적 컨텐츠~~ 를 제공하는 프로그램  
  ex) IIS, Apache Server, Nginx
    * **정적 컨텐츠?** 어느 사용자 요청이든 항상 동일한 컨텐츠를 제공 (html, css, image 등)
* 사실 **웹서버는 컨텐츠가 정적인지 동적인지는 전혀 상관없다.**
* 그저 요청이 오면 **html 코드로 응답해주는 역할** 을 할 뿐이다.
  * 정적파일이라면 파일내용 그대로 전달해주고 동적파일이라면 인터프리터가 html 코드로 해석된 결과를 전달해준다.

* **정리**
>  * 웹 브라우저(클라이언트)로부터 `http 요청`을 받을 수 있다.
>  * 정적 컨텐츠 요청 시, 정적컨텐츠를 제공할 수 있다.
>  * 동적 컨텐츠 요청 시
>    * WAS로 전달하여 WAS가 처리한 결과를 클라이언트에게 전달하여 제공한다.
>    * `IIS + ASP` 또는 `Apache + PHP` 조합으로도 WAS없이 동적페이지 구성 가능하다.  
      (하지만 보안이나 트래픽 문제로 사장된 구조)

# WAS - Web Application Server
* DB 조회나 다양한 로직 처리를 요구하는 동적 컨텐츠 제공하는 프로그램 (정적 컨텐츠도 당연 가능)
  ex) Tomcat, JBoss, Jeus
    * **동적 컨텐츠?** 요청 인자에 따라 바뀔 수 있는 컨텐츠  
* Web Server + Web Container
    * Web Server: 정적 컨텐츠
      * 요청을 받아 Container로 전송
      * 결과값을 받아 클라이언트에게 전송
    * Web Container: 비즈니스 로직이나 DB 조회같은 동적인 컨텐츠 제공
      * JSP, Servlet 구동 환경 제공
* application
  * 빌드나 컴파일된 파일(바이트코드나 기계어로 미리 생성되어 있는 상태 - ex) `.class`나 `.exe`)  
  👉 따라서 서버사이드 스크립트 언어(ASP 나 PHP)를 구동하는건 WAS가 아니라 WebServer다.

* **정리**
>  * 웹 브라우저(클라이언트)로부터 `http 요청`을 받을 수 있다. (대부분의 WAS는 Web Server를 내장하고 있다.)
>  * 정적 컨텐츠 요청 시, 정적컨텐츠를 제공할 수 있다.
>  * 동적 컨텐츠 요청 시, 다양한 로직처리를 통해 동적컨텐츠를 제공할 수 있다.


<br/>

보통 실무에서는 보안/트래픽 문제로 Web Server - WAS - DB, 3 Tier 구조로 서버를 구성하고 있다.    
(PHP의 경우, Apache/Nginx - PHP-FPM - DB, 3 Tier 구조로 구성이 된다고 한다.)   

### Web Service Architecture
![WebServiceArchitecture](https://github.com/ChaerinYu/Today-I-Learned/blob/main/Network/images/web_service.png?raw=true)


## 왜 굳이 Web Server와 같이 사용할까? 🤔
1. 책임 분할을 통한 서버 부하 방지 가능
  * 역할 분배 가능 - 정적 컨텐츠는 Web Server가 동적 컨텐츠는 WAS가 담당한다.
2. 여러 WAS Load Balancing
  * WAS가 처리해야 하는 요청을 여러 WAS가 나누어서 처리할 수 있도록 한다.
  * Health Check 기능
    * 로드밸런싱할 때 WAS들이 제대로 동작이 되는지 확인해준다.
      * 서버에 주기적으로 http 요청을 보내 서버 상태를 확인한다. 
      * 특정 URL 요청에 200 응답 오는지 확인하여 그렇지 않은 경우 해당 서버로 요청을 보내지 않도록 설정이 가능하다.
      * `interval` (서버 확인 요청 보내는 주기 - default: 5 sec)
      * `fails` (n번 연속 실패시 비정상으로 인지하여 자동으로 차단 - default: 1)
      * `passes` (서버 다시 복구된 후 요청이 n번 연속 성공시 정상으로 인지 - default: 1) 
3. 보안 강화
   * 리버스 프록시를 통해 실제 서버를 외부에 노출하지 않을 수 있다.
     * **리버스 프록시?** 네트워크 서비스에 간접적으로 접속할 수 있게 해 주는 컴퓨터 시스템이나 응용 프로그램
   * WAS같은 경우 DB 접근 권한을 갖고 있을 수 있어서 위험할 수 있는데 리버스 프록시를 통해 방지할 수 있다.


<br/>

### ✔ 결론
> WAS만으로도 서비스는 가능하지만 **서비스 확장성과 안정성**을 고려한다면 Web Server와 함께 사용하는 게 좋아 보인다.

<br/><br/><br/>


[참고](https://www.youtube.com/watch?v=mcnJcjbfjrs)
