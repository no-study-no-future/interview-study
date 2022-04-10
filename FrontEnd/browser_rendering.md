# 브라우저 렌더링

- 브라우저는 서버에 요청을 하고 서버에서 보내준 응답을 받아 브라우저 화면에 렌더링하여 클라이언트에게 웹페이지를 제공한다.  
- 브라우저의 **렌더링 엔진**은 응답으로 받은 html 파일을 해석하여 `DOM 트리`와 `CSSOM 트리`를 만들고 이 둘을 결합하여 `Render 트리`를 만든다.
  - **DOM(Document Object Model)**  
  : html파일을 브라우저가 이해할 수 있고 메모리에 보관할 수 있는 object로 변환시키는데 object로 변환된 후 tree 형태로 구성되어 있다.
  - **CSSOM(CSS Object Model)**  
  : 개발자가 정의한 CSS뿐만 아니라 브라우저에서 기본적으로 설정되어 있는 CSS를 cascading rule에 따라 병합하여 만들어진다.   
  - **Render Tree**  
    - 브라우저가 html을 읽어 `DOM 트리`를 만들고 CSS 파일을 읽은 다음 모든 CSS를 계산하여 `CSS 스타일 트리`를 만든다.  
    그러고 나서 브라우저에 표시될 요소들만 `Render 트리`에 선별해서 표시한다.  
    `DOM + CSSOM 👉 Render Tree`  
    - DOM트리의 `html`, `head`, `meta`등의 비 시각적 요소나 `display: none` 속성이 적용된 요소들은 Render Tree에서 제외된다.
- 브라우저에서 Render 트리 기반으로 UI가 그려진다.

<br/><br/>

### https://www.naver.com/ 에 접속해보자
1. 브라우저는 해당 주소로 네트워크 요청을 보내는데 이 요청은 DNS 서버에 들린다.
   - **DNS(Domain Name Server)**  
     : 192.168.0.0 같은 IP 주소는 사람들이 인지하기도 기억하기도 어렵다.  
     `www.naver.com`과 같이 사람이 읽기 쉽고 기억하기 쉬운 단어들로 이루어진 주소를 사용하는데 이를 도메인이라고 한다.  
     이 도메인 주소를 IP와 매핑해 놓은 서버가 있는데 이 서버가 DNS 서버다.
2. 브라우저 요청이 DNS 서버에서 해당 IP를 찾아 네이버 서버로 가게 되며 네이버는 위 주소에 해당하는 리소스를 응답으로 보내준다.
   - 개발자도구 Network 탭 - Response에서 확인 가능하다. 

<br/><br/>

## 렌더링 엔진
- html 파일을 처음부터 한 줄씩 읽고 해석하여 브라우저가 이해할 수 있는 DOM트리 생성 👉 html 파싱
- html 파싱 도중 CSS 파일 로드하는 `<link>`/`<style>` 태그 만나면 CSS 파일을 서버에 요청하여 CSSOM 트리 생성 👉 CSS 파일 파싱

### 만약 CSS 파싱이 html 파싱보다 더 늦게 끝나면? 🤔
- 아무 스타일도 없는 모습의 UI가 화면에 보인다.
- `<link>` 태그에 걸려 있는 외부 CSS 파일의 용량이 크거나 인터넷 연결이 좋지 않아 html 파싱 후, CSS 파일이 뒤늦게 파싱되어 렌더링 트리가 재구성되면서 발생
  - 렌더링 엔진은 html을 위에서부터 파싱하기 때문에 CSS 관련 링크나 style 태그는 html 문서 상단에 (보통 `<head>` 태그 사이) 위치시키는 게 좋다.

### `<script>` 태그 적절한 위치 🤔

> html 파싱 도중 `<script>` 태그를 만나게 되면 html 파싱을 멈추고 스크립트를 다운로드 한다.  
> 스크립트 로딩이 완료되면 자바스크립트 엔진이 스크립트를 실행한다.   
> 이 과정이 끝나면 렌더링 엔진은 html 파싱이 중단된 곳으로 돌아가 html 파싱을 계속하게 된다.   

- 따라서 `<script>` 태그 만나면 `html` 파싱이 중단되기 때문에 `<script>` 태그의 위치도 중요하다.
``` html
<html>
    <head>
        <link rel="stylesheet" href="style.css">
        <script src="big-size.js"></script>   <!-- (A) -->
    </head>
    <body>
    	<div id="root"></div>  <!-- (B) -->
    </body>
</html>
```
- html 파싱을 진행하다가 `<link>` 태그에서 `style.css` 로드하고 CSSOM 트리를 구성한다.
  - CSSOM 구성하는 동안 html 파싱은 멈추지 않고 (A)에서 `<script>` 태그를 만나게 된다.
- `<script>` 태그를 만나게 되면 html 파싱은 멈추고 `big-size.js`를 로딩한다.
  - html 파싱이 멈췄기 때문에 화면엔 (B)가 보이지 않는다. 
  - 스크립트에서 (B) `<div>`에 접근하려고 하면 오류가 발생한다.

### 🤓 해결 방법
#### 1. 로딩이 오래 걸리는 스크립트나 html 요소에 접근해야 하는 코드가 있는 스크립트는 body의 닫는 태그 바로 위에 위치시킨다.
``` html
<html>
    <head>
        <link rel="stylesheet" href="style.css">
    </head>
    <body>
    	<div id="root"></div>
        <script src="big-size.js"></script>
    </body>
</html>
```

#### 2. 스크립트의 `defer` 속성 (feat. `async` 속성)
``` html
<script src="A.js" defer></script>
<script src="B.js" async></script>
```
- 브라우저는 html 파싱을 멈추지 않고 스크립트 로딩과 파싱을 동시에 진행하게 된다. 즉, **스크립트 로딩이 비동기적으로 수행된다.**
  - `defer`와 `async` 두 속성의 차이는 스크립트의 실행 시점
- **`defer`**
  - 스크립트 로딩이 완료되어도 실행되지 않고 html 문서가 모두 파싱될 때까지 기다렸다가 파싱이 완료되면 그 때 실행된다.
  - 모듈 스크립트 `<script type="module"></script>` 는 defer 속성이 기본값
- **`async`**
  - html 파싱 완료 여부와 상관없이 스크립트 로딩되면 바로 스크립트 실행한다.
  - `async` 속성의 스크립트 내에서 어떤 html 요소에 접근하는 코드가 있을 때, 해당 html 요소가 파싱되지 않았다면 에러 발생한다.

<br/><br/><br/>

## Reflow와 Repaint
- reflow와 repaint는 DOM 요소가 시각적으로 변경됐을 때, 이 변화를 다시 계산하고 화면에 그려주는 작업이다.

### Reflow
- Layout 단계
- DOM 요소의 기하학적 속성이 변경될 때 / 브라우저 사이즈가 변할 때 / 스타일 시트가 로딩되었을 때 발생하는 변화들을 다시 계산해주는 작업
- 변화된 요소의 주변 모든 요소 (부모/자식/형제)들도 영향을 받게 된다. 👉 결국 하나의 DOM 요소의 시각적 변화가 **DOM 트리 전체에 대해 다시 계산을 수행한다.**
- 이런 계산을 통해 Render Tree가 업데이트되고 repaint가 실행되어 화면에 그려준다. 👉 엄청난 비용 발생
- 직접적으로 시각적 속성 바꿔주는 행위 말고도 단순히 어떤 메소드 호출하거나 프로퍼티 값을 가져오는 것만으로도 리플로우가 발생하는 경우가 있다.
  - 보통 계산된 스타일 값(computed style) 조회하는 경우이다. (ex. width: 50%) 
  - [참고](https://gist.github.com/paulirish/5d52fb081b3570c81e3a)

### Repaint
- 변경된 요소를 실제로 화면에 그려주는 작업
- 리플로우 발생 시 필연적으로 리페인트가 발생한다.
- 요소들을 계산해주는 리플로우 보다는 상대적으로 가벼운 작업이다.
- `background-color`, `visiblity` 수정 등..
- 개발자도구 Rendering 탭의 Paint flashing 기능: Chrome에서 repaint가 실시간으로 발생하는 부분 하이라이트 해주는 기능

<br/>

## Reflow와 Repaint 줄이는 방법 🤔
- 무거운 작업 : Reflow > Repaint
- Reflow 발생 시, Repaint도 무조건 발생
- 👉 Reflow 최소한으로 발생되게 만들기

### 1. DOM 속성 변경 코드 그룹핑하기
``` javascript
var h1 = div1.clientHeight;
div1.style.height = h1 + 10 + 'px';

var h2 = div2.clientHeight;
div2.style.height = h2 + 10 + 'px';

var h3 = div3.clientHeight;
div3.style.height = h3 + 10 + 'px';
```
- Reflow(Layout) 3번 발생

``` javascript
var h1 = div1.clientHeight;
var h2 = div2.clientHeight;
var h3 = div3.clientHeight;

div3.style.height = h3 + 10 + 'px';
div2.style.height = h2 + 10 + 'px';
div1.style.height = h1 + 10 + 'px';
```
- Reflow(Layout) 1번 발생

👉 브라우저는 기본적으로 reflow를 줄이기 위한 전략을 갖고 있다.  
- 요소 변경을 당장 처리하지 않고 queue에 저장해뒀다가 일정 시간이 지나거나 처리할 작업이 일정량 쌓인 경우 reflow를 실행한다.
- 2번째 예시의 경우, 높이 변화를 queue에 쌓아뒀다가 일정 시간 지난 후 한 번에 처리했기 때문에 reflow가 1번 발생했다.


### 2. Reflow 유발시키는 메서드/프로퍼티는 변수에 저장하여 사용하기
``` javascript
// 나쁜 예
for(let i=1; i<10; i++){
    div.style.left = (div1.getBoundingClientRect().left + i) + 'px';
}

// 좋은 예
let {left} = div1.getBoundingClientRect();  // reflow 유발 메서드는 변수에 저장해 사용 
for(let i=1; i<10; i++){
    div.style.left = (left + i) + 'px';
    left += i;
}
```

### 3. CSS 수정은 한 번에 변경하기
- class명 수정하여 css 수정을 해주거나 `cssText` 속성을 이용한다.
``` html
<style>
  .my-div {
      width: 100px;
      height: 100px;
      padding: 5px;
      border: 5px solid blue;
      background-color: black;
      color: white;
  }
</style>
<body>
  <div id="div"></div>

  <script>
      // 나쁜 예
      div.style.width = "100px";   		  // reflow, repaint
      div.style.height = "100px";   		  // reflow, repaint
      div.style.padding = "5px";   		  // reflow, repaint
      div.style.border = "5px solid blue";      // reflow, repaint
      div.style.backgroundColor = "black";      // repaint
      div.style.color = "white";		  // repaint

      // 좋은 예1
      div.className = "my-div";

      // 좋은 예2
      div.style.cssText = "width: 100px; height: 100px; ...";
  </script>
</body>
```

### 4. 여러 속성 변경 시, `display: none` 적용 후 작업하기
- `display : none` 이 적용되면 그 요소는 렌더 트리에서 제외되고 없는 요소 취급당하기 때문에 
이 요소에 다른 기하학적  변화가 일어나도 reflow나 repaint는 발생하지 않게 된다.
``` javascript

div.style.display = "none";   // (A)

// ... 여러 스타일 속성 수정 ...

div.style.display = "block";  // (B)
 
```
이렇게 코드를 작성해 주면 (A) 와 (B) 에서 reflow, repaint가 각각 한 번 씩 발생하므로
그 사이에 아무리 많은 스타일 값을 변경해줘도 총 2번의 reflow, repaint만 발생하게 될것이다.
 
 
<br/><br/><br/><br/><br/><br/>

- [참고](https://kwangsunny.tistory.com/42?category=978480)