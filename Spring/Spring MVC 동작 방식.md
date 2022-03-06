# Spring MVC 동작 방식
## 요약
- Spring MVC는 DespatcherServlet, HandlerMapping, HandlerAdapter, ViewResolver로 동작한다
- Spring MVC Annotation을 알아보자

## Spring MVC 핵심 구성 요소
### DispatcherServlet
- 스프링 부트 구동 시 DispatcherServlet을 서블릿으로 자동등록(urlPatterns=“/“)
- 클라이언트로부터 어떠한 요청이 들어오면 Tomcat과 같은 서블릿 컨테이너가 요청을 받음 -> 이 모든 요청을 DispatcherServlet이 받게 됨
- 즉, HTTP 프로토콜로 들어오는 모든 요청을 처리 -> 장점
> 모든 요청을 가장 먼저 받기 때문에 Front Controller이라고도 함  

### HandlerMapping
- 클라이언트의 요청을 처리할 컨트롤러 탐색
- URL에 매핑된 핸들러(컨트롤러) 조회

### HandlerAdapter
- 실제 핸들러를 실행시키며, 핸들러의 처리 결과를 ModelAndView로 변환

### ViewResolver
- 컨트롤러 처리 결과를 JSP를 이용해서 생성하기 위해 사용
- View의 논리 이름을 물리 이름으로 바꾸고 렌더링 역할을 담당하는 뷰 객체 반환

> Handler(핸들러)? : 웹 요청을 실제로 처리하는 객체, Spring MVC입장에서 @Controller 객체나 Controller 인터페이스를 구현한 객체 모두를 말함.  

### 스프링 MVC 동작 순서
![스프링 MVC 동작](https://github.com/leeejuhyeong/images/blob/main/no-study-no-future/Spring/%E1%84%89%E1%85%B3%E1%84%91%E1%85%B3%E1%84%85%E1%85%B5%E1%86%BCMVC%E1%84%83%E1%85%A9%E1%86%BC%E1%84%8C%E1%85%A1%E1%86%A8.jpg?raw=true)  
  
### RestController
![RestController 동작](https://github.com/leeejuhyeong/images/blob/main/no-study-no-future/Spring/restcontroller%E1%84%83%E1%85%A9%E1%86%BC%E1%84%8C%E1%85%A1%E1%86%A8.jpg?raw=true)  
  
- 1 ~ 5는 Spring MVC와 동일
- 6. 컨트롤러가 ResponseEntity를 반환
- 7. HandlerAdapter가 반환받은 ResponseEntity를 통해 Response 처리 진행
- 8. 서버의 응답을 클라이언트로 반환

## 요청의 흐름
### Servlet
- 서블릿 호출 시 HttpServlet이 제공하는 service() 메서드 호출
- service()를 시작으로 여러 메소드가 실행, DispatcherServlet.doDispatch()가 호출

### DispatcherServlet
```
protected void doDispatch(HttpServletRequest request, HttpServletResponse response) throws Exception {
	HttpServletRequest processedRequest = request;
	HandlerExecutionChain mappedHandler = null;
	boolean multipartRequestParsed = false;

	try {
		ModelAndView mv = null;
		Exception dispatchException = null;

		try {
			processedRequest = checkMultipart(request);
			multipartRequestParsed = (processedRequest != request);

			// 1. 핸들러 조회
			mappedHandler = getHandler(processedRequest);
			if (mappedHandler == null) {
				noHandlerFound(processedRequest, response);
				return;
			}

			// 2. 핸들러 어뎁터 조회
			HandlerAdapter ha = getHandlerAdapter(mappedHandler.getHandler());

			// 3. 헨들러 어댑터 실행 -> 4. 핸들러 어댑터를 통해 핸들러 실행 -> 5. ModelAndView 반환
			mv = ha.handle(processedRequest, response, mappedHandler.getHandler());
		}
		catch (Exception ex) {
			dispatchException = ex;
		}
		
		processDispatchResult(processedRequest, response, mappedHandler, mv, dispatchException);
		// ........이하 생략 
	}
}

private void processDispatchResult(HttpServletRequest request, HttpServletResponse response,
		@Nullable HandlerExecutionChain mappedHandler, @Nullable ModelAndView mv,
		@Nullable Exception exception) throws Exception {

	boolean errorView = false;
	// 7. 뷰 렌더링 호출
	render(mv, request, response);
}

protected void render(ModelAndView mv, HttpServletRequest request, HttpServletResponse response) throws Exception {
	// Determine locale for request and apply it to the response.
	Locale locale =
			(this.localeResolver != null ? this.localeResolver.resolveLocale(request) : request.getLocale());
	response.setLocale(locale);

	View view;
	String viewName = mv.getViewName();
	// 8. 뷰 렌더링
	view.render(mv.getModelInternal(), request, response);
}


```
  
## Spring MVC Annotation
### @RequestMapping
- HandlerMapping과 HandlerAdapter의 역할
- 요청 정보를 매핑, 해당 URL 호출되면 이 메소드가 호출

### @GetMapping, @PostMapping, @PutMapping, @DeleteMapping
- Rest API 형식의 CRUD(c = p, r = g, u = p, d = d)
- @RequestMapping의 세부 url 매핑

### @RequestBody
- **Json(application/json)형태**의 HTTP Body 내용을 Java Object로 변환
- 데이터는 MessageConvert를 통해 Java객체로 변환
- 객체의 기본 생성자를 통해 객체를 생성하고, 내부적으로 Reflection을 사용해 값을 할당하므로 값을 주입하기 위한 **생성자나 Settr가 필요 없음**
> 데이터를 Java 객체로 변환하기 때문에 값 주입 생성자나, Setter가 필요 없음  

### @RequestParam
- 1개의 HTTP 요청 파라미터를 받기 위해 사용, 파라미터 이름으로 바인딩
- @RequestParam(“userid”) String userID -> request.getparameter(“userid”)
- 선언 시 필수적으로 보내야하며, 필수 변수가 아니라면 required = false로 설정 가능

### @ModelAttribute
- **multipart/form-data 형태**의 HTTP Body 내용과 HTTP 파라미터의 값들을 **생성자나 Setter를 통해** 주입해줌
- HTTP Body 데이터와 HTTP 파라미터들을 Setter를 통해 1대1로 객체에 바인딩
- Reflection으로 검증하기 때문에 모든 값을 받는 생성자가 없다면, 이외의 값은 Setter로 주입됨
> 데이터를 1대1로 객체에 바인딩하기 때문에 값 주입 생성자나, Setter가 필요  

### @RequestPart
- multipart/form-data 형태의 요청이 올 때 사용
- File과 DTO를 같이 받을 수 있음
```
@PostMapping("")
public String insertUser(
		@RequestPart(value = "file") MultipartFile multipartFile,
		@RequestPart(value = "userInfo") UserInfo userInfo) {
}
```

### @PathVariable
- URL의 파라미터를 매핑

### @ResponseBody
- 해당 메소드의 결과를 ModelAndView가 아닌 JSON으로 반환

### @RestController
- 컨트롤러의 모든 메소드를 View가 아닌 Json으로 반환





## 출처
- [스프링 MVC 1편  - 백엔드 웹 개발 핵심 기술 - 인프런 | 강의](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-mvc-1/dashboard)
- [SpringDispatcher-Servlet(디스패처 서블릿)이란? 디스패처 서블릿의 개념과 동작 과정 - MangKyu’s Diary](https://mangkyu.tistory.com/18)
- [Spring MVC 기본 어노테이션과 Lombok 정리하기 :: Gyun’s 개발일지](https://devlog-wjdrbs96.tistory.com/401)
- [Spring Annotation 정리](https://velog.io/@gillog/Spring-Annotation-%EC%A0%95%EB%A6%AC)