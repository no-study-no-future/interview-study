# 정규화 (Normalization)

### ✔ 요약
> 정규화는 관계형 DB의 설계 단계에서 **데이터 중복을 최소화**하기 위하여 데이터 구조를 결정하는 작업으로
> **무결성(데이터의 정확성과 일관성)을 유지**할 수 있으며 중복 데이터 제거로 **DB 저장 용량도 줄일 수 있습니다**. 


* 중복 데이터를 허용하지 않기 때문에 `무결성(Integrity)`를 유지할 수 있으며, `DB의 저장 용량` 도 줄일 수 있다.  
* 테이블과 테이블 간의 관계를 분리하여 체계적으로 만드는 단계 👉 **정규화 (Normalization)**
* 현실적으로 모든 릴레이션을 반드시 5NF에 속하도록 분해할 필요는 없다.


* 정규화를 거치지 않은 경우 `이상(Anomaly) 현상`이 발생할 수 있다.
  * 데이터들이 중복되어 발생하는 문제
    * 삽입 이상 (Insertion Anomaly): 데이터 삽입 시 의도와 다른 값들도 삽입됨
    * 삭제 이상 (Delete Anomaly): 데이터 삭제 시 의도와 다른 값들도 삭제됨
    * 갱신 이상 (Update Anomaly): 속성값 갱싱 시 일부 튜플만 갱신되어 모순 발생

### 정규화 목적
* 중복을 제거하여 삽입, 삭제, 갱신 이상 발생 방지
* 중복 데이터 제거를 통한 저장공간 효율화
* 자료의 불일치성 최소화하여 데이터 무결성 확보

### 정규화 원리
* 분리의 원리`principle of separation`
  * 하나의 독립된 관계성은 별도의 릴레이션으로 분리시켜 표현한다.
* 정보 표현의 무손실`nonless representation of information`
  * 변환된 스키마가 표현하는 정보는 변환되기 전 정보를 모두 포함하고 있어야 한다.
* 최소의 데이터 중복`minimal data redundancy`
  * 중복으로 인한 여러가지 이상현상이 제거되어야 한다.

<br/><br/>

😶💭 각 정규형은 이전 정규형을 만족한 상태여야 합니다.

### 제 1 정규화 (First Normal Form: 1NF)
* 테이블 컬럼이 원자값`atomic value`을 갖도록 분해한다.
* 1NF를 만족하지 않는 릴레이션
<table>
  <tbody>
    <tr>
      <th>과목</th>
      <th>수강생</th>
    </tr>
    <tr>
      <td>국어</td>
      <td>유채린, 김학생</td>
    </tr>
    <tr>
      <td>수학</td>
      <td>홍길동</td>
    </tr>
    <tr>
      <td>영어</td>
      <td>유채린, 홍길동</td>
    </tr>
  </tbody>
</table>

* 발생할 수 있는 Anomaly
  * 갱신이상: '유채린'이 수강하는 '영어'를 '과학'으로 업데이트하면 '홍길동'이 듣고있는 과목도 '과학'으로 업데이트 된다.
  * 삭제이상: '유채린'이 '영어' 과목 수강 취소시, '홍길동'의 수강정보도 삭제된다.
* 1NF 만족하는 릴레이션
<table>
  <tbody>
    <tr>
      <th>과목</th>
      <th>수강생</th>
    </tr>
    <tr>
      <td>국어</td>
      <td>유채린</td>
    </tr>
    <tr>
      <td>국어</td>
      <td>김학생</td>
    </tr>
    <tr>
      <td>수학</td>
      <td>홍길동</td>
    </tr>
    <tr>
      <td>영어</td>
      <td>유채린</td>
    </tr>
    <tr>
      <td>영어</td>
      <td>홍길동</td>
    </tr>
  </tbody>
</table>

### 제 2 정규화 (Second Normal Form: 2NF)
* `완전 함수 종속`이 되도록 릴레이션을 분해해야 한다.
* 부분집합이 결정자가 되어서는 안된다.
* 2NF를 만족하지 않는 릴레이션
<table>
  <tbody>
    <tr>
      <th>학번</th>
      <th>이름</th>
      <th>학과</th>
      <th>학과장</th>
    </tr>
    <tr>
      <td>1234</td>
      <td>유채린</td>
      <td>소프트웨어학과</td>
      <td>김교수</td>
    </tr>
    <tr>
      <td>2345</td>
      <td>홍길동</td>
      <td>경영학과</td>
      <td>한교수</td>
    </tr>
    <tr>
      <td>3456</td>
      <td>김학생</td>
      <td>전자과</td>
      <td>박교수</td>
    </tr>
    <tr>
      <td>4567</td>
      <td>이디비</td>
      <td>소프트웨어학과</td>
      <td>김교수</td>
    </tr>
  </tbody>
</table>
  
  * 학번 ↔ 이름, 학과, 학과장는 종속관계
    * 학과번호 ↔ 학과, 학과장은 부분적인 종속관계 
    * 소속학과와 학과장은 이 릴레이션에 꼭 있을 필요가 없다. 👉 중복 적재 (소프트웨어학과 김교수)
    
* 발생할 수 있는 Anomaly
  * 삽입이상: '소프트웨어학과', '경영학과', '전자과' 학생 추가시 불필요한 중복정보 추가된다. 👉 `학과장`
  * 갱신이상: '소프트웨어학과' 학과장 수정 시 다 찾아서 변경해야 한다.
  * 삭제이상: '김학생' 자퇴 시 '전자과' 학과장의 정보가 사라진다.

* 2NF 만족하는 릴레이션
<table>
  <tbody>
    <tr>
      <th>학번</th>
      <th>이름</th>
      <th>학과</th>
    </tr>
    <tr>
      <td>1234</td>
      <td>유채린</td>
      <td>소프트웨어학과</td>
    </tr>
    <tr>
      <td>2345</td>
      <td>홍길동</td>
      <td>경영학과</td>
    </tr>
    <tr>
      <td>3456</td>
      <td>김학생</td>
      <td>전자과</td>
    </tr>
    <tr>
      <td>4567</td>
      <td>이디비</td>
      <td>소프트웨어학과</td>
    </tr>
  </tbody>
</table>
<table>
  <tbody>
    <tr>
      <th>학과</th>
      <th>학과장</th>
    </tr>
    <tr>
      <td>소프트웨어학과</td>
      <td>김교수</td>
    </tr>
    <tr>
      <td>경영학과</td>
      <td>한교수</td>
    </tr>
    <tr>
      <td>전자과</td>
      <td>박교수</td>
    </tr>
    <tr>
      <td>소프트웨어학과</td>
      <td>김교수</td>
    </tr>
  </tbody>
</table>

### 제 3 정규화 (Thrid Normal Form: 3NF)
* `이행적 함수 종속`을 제거해야 한다.
  * `이행적 함수 종속`: A ➡ B이고 B ➡ C일 때, A ➡ C인 관계
* 3NF를 만족하지 않는 릴레이션

<table>
  <tbody>
    <tr>
      <th>학번</th>
      <th>이름</th>
      <th>학과</th>
      <th>대학</th>
    </tr>
    <tr>
      <td>1234</td>
      <td>유채린</td>
      <td>소프트웨어학과</td>
      <td>공대</td>
    </tr>
    <tr>
      <td>2345</td>
      <td>홍길동</td>
      <td>경영학과</td>
      <td>경상</td>
    </tr>
    <tr>
      <td>3456</td>
      <td>김학생</td>
      <td>전자과</td>
      <td>공대</td>
    </tr>
    <tr>
      <td>4567</td>
      <td>이디비</td>
      <td>소프트웨어학과</td>
      <td>공대</td>
    </tr>
  </tbody>
</table>

* 발생할 수 있는 Anomaly
  * 삽입이상: '소프트웨어학과' 학생 추가시 불필요한 중복정보 추가된다. 👉 `대학`
  * 갱신이상: '소프트웨어학과' 소속 대학 'IT'로 변경 시 다 찾아서 변경해야 한다.
  * 삭제이상: '김학생' 자퇴 시 '전자과' 대학 정보가 사라진다.

* 3NF 만족하는 릴레이션

<table>
  <tbody>
    <tr>
      <th>학번</th>
      <th>이름</th>
      <th>학과</th>
    </tr>
    <tr>
      <td>1234</td>
      <td>유채린</td>
      <td>소프트웨어학과</td>
    </tr>
    <tr>
      <td>2345</td>
      <td>홍길동</td>
      <td>경영학과</td>
    </tr>
    <tr>
      <td>3456</td>
      <td>김학생</td>
      <td>전자과</td>
    </tr>
    <tr>
      <td>4567</td>
      <td>이디비</td>
      <td>소프트웨어학과</td>
    </tr>
  </tbody>
</table>
<table>
  <tbody>
    <tr>
      <th>학과</th>
      <th>대학</th>
    </tr>
    <tr>
      <td>소프트웨어학과</td>
      <td>공대</td>
    </tr>
    <tr>
      <td>경영학과</td>
      <td>경상</td>
    </tr>
    <tr>
      <td>전자과</td>
      <td>공대</td>
    </tr>
  </tbody>
</table>


### BCNF (Boyce-Codd Normal Form)
* 모든 결정자가 후보키가 되도록 분해해야 한다.
  * 결정자가 후보키가 아닌 속성을 제거해야 한다.  
* BCNF 만족하지 않는 릴레이션

<table>
  <tbody>
    <tr>
      <th>학번</th>
      <th>강의명</th>
      <th>교수</th>
    </tr>
    <tr>
      <td>1234</td>
      <td>소프트웨어공학</td>
      <td>김교수</td>
    </tr>
    <tr>
      <td>2345</td>
      <td>경영학개론</td>
      <td>금교수</td>
    </tr>
    <tr>
      <td>2345</td>
      <td>마케팅이론</td>
      <td>최교수</td>
    </tr>
    <tr>
      <td>4567</td>
      <td>자료구조</td>
      <td>정교수</td>
    </tr>
  </tbody>
</table>

* 기본키`Primary key`는 '학번'과 '강의명'이다. '학번'과 '강의명'은 '교수'를 결정하고 있고, '교수'는 '강의명'을 정하고 있다.
* '교수'는 '강의명'을 저장하는 결정자이지만 후보키`Candidate key`는 아니다.

* 발생할 수 있는 Anomaly
  * 삽입이상: '1234' 학생이 '자료구조' 수강하는 경우 불필요한 중복정보 추가된다. 👉 `교수`
  * 갱신이상: '정교수' 의 담당과목을 '알고리즘'으로 바꾸는 경우 '4567' 학생의 수강과목이 바뀐다.
  * 삭제이상: '4567' 학생 자퇴 시 '자료구조' 과목을 담당하는 '정교수' 정보가 사라진다.


* BCNF 만족하는 릴레이션

<table>
  <tbody>
    <tr>
      <th>학번</th>
      <th>강의명</th>
    </tr>
    <tr>
      <td>1234</td>
      <td>소프트웨어공학</td>
    </tr>
    <tr>
      <td>2345</td>
      <td>경영학개론</td>
    </tr>
    <tr>
      <td>2345</td>
      <td>마케팅이론</td>
    </tr>
    <tr>
      <td>4567</td>
      <td>자료구조</td>
    </tr>
  </tbody>
</table>

<table>
  <tbody>
    <tr>
      <th>강의명</th>
      <th>교수</th>
    </tr>
    <tr>
      <td>소프트웨어공학</td>
      <td>김교수</td>
    </tr>
    <tr>
      <td>경영학개론</td>
      <td>금교수</td>
    </tr>
    <tr>
      <td>마케팅이론</td>
      <td>최교수</td>
    </tr>
    <tr>
      <td>자료구조</td>
      <td>정교수</td>
    </tr>
  </tbody>
</table>

### 제 4 정규화 (Fourth Normal Form: 4NF)
* 다치 종속성`Multi-Valued Dependency`을 제거해야 한다.
  * _두 개의 독립된 애트리뷰트가 1:N 관계로 대응하는 관계로, '↠'화살표로 종속성을 표시한다._ 
  * [다치 종속성](https://itwiki.kr/w/%EB%8B%A4%EC%B9%98_%EC%A2%85%EC%86%8D)
* 4NF를 만족하지 않는 릴레이션

<table>
  <tbody>
    <tr>
      <th>과목</th>
      <th>교수</th>
      <th>교재</th>
    </tr>
    <tr>
      <td>데이터베이스</td>
      <td>김교수</td>
      <td>DB 책1</td>
    </tr>
    <tr>
      <td>데이터베이스</td>
      <td>김교수</td>
      <td>DB 책2</td>
    </tr>
    <tr>
      <td>데이터베이스</td>
      <td>백교수</td>
      <td>DB 책1</td>
    </tr>
    <tr>
      <td>데이터베이스</td>
      <td>백교수</td>
      <td>DB 책2</td>
    </tr>
    <tr>
      <td>자료구조</td>
      <td>정교수</td>
      <td>DATA STRUCTURE 1</td>
    </tr>
    <tr>
      <td>자료구조</td>
      <td>정교수</td>
      <td>DATA STRUCTURE 2</td>
    </tr>
    <tr>
      <td>자료구조</td>
      <td>정교수</td>
      <td>DATA STRUCTURE 3</td>
    </tr>
  </tbody>
</table>

* `'과목' -->> '교수' | '교재'`
* '교수' | '교재' 가 '과목' 에 다치 종속되고 '과목'이 '교수' | '교재'를 다치 결정한다.


* 발생할 수 있는 Anomaly
  * 삽입이상: '데이터베이스' 교재 추가시 레코드를 1개가 아닌 2개를 추가해야 된다. ('김교수', '백교수' 하나씩)
  * 갱신이상: '자료구조' 교수 변경 시 3개의 튜플을 수정해야 한다.
  * 삭제이상: '정교수' 퇴직 시 '자료구조'-'교재' 정보가 사라진다.


* 4NF 만족하는 릴레이션
*
  * 과목 교수
<table>
  <tbody>
    <tr>
      <th>과목</th>
      <th>교수</th>
    </tr>
    <tr>
      <td>데이터베이스</td>
      <td>김교수</td>
    </tr>
    <tr>
      <td>데이터베이스</td>
      <td>백교수</td>
    </tr>
    <tr>
      <td>자료구조</td>
      <td>정교수</td>
    </tr>
  </tbody>
</table>

*
  * 과목 교재
<table>
  <tbody>
    <tr>
      <th>과목</th>
      <th>교재</th>
    </tr>
    <tr>
      <td>데이터베이스</td>
      <td>DB 책1</td>
    </tr>
    <tr>
      <td>데이터베이스</td>
      <td>DB 책2</td>
    </tr>
    <tr>
      <td>자료구조</td>
      <td>DATA STRUCTURE 1</td>
    </tr>
    <tr>
      <td>자료구조</td>
      <td>DATA STRUCTURE 2</td>
    </tr>
    <tr>
      <td>자료구조</td>
      <td>DATA STRUCTURE 3</td>
    </tr>
  </tbody>
</table>

### 제 5 정규화 (Fifth Normal Form: 5NF)
* 조인 종속성`Join Dependency`을 제거해야 한다.
  * _릴레이션이 그의 어떤 프로젝션들을 조인한 결과와 똑같아야 한다는 제약 조건_
    * `프로젝션`이란 속성을 구하는 연산으로 수직적 부분 집합을 취하는 연산
    * `조인`이란 2개 이상의 릴레이션을 조건에 따라 접속하는 연산
* 이전 정규형 위배하는 함수적 종속성이 존재하지 않고 다치종속성도 존재하지 않는다.
* 릴레이션을 2 개의 스키마로 무손실 분해를 할 수는 없지만 2 개 이상의 릴레이션 스키마로 무손실 분해를 할 수 있다.

[제 4 정규화](#제_4_정규화_(Fourth_Normal_Form:_4NF)) 후 조인 연산한 다음 조인 종속성을 제거해주면 된다.

<table>
  <tbody>
    <tr>
      <th>과목</th>
      <th>교수</th>
      <th>교재</th>
    </tr>
    <tr>
      <td>데이터베이스</td>
      <td>김교수</td>
      <td>DB 책1</td>
    </tr>
    <tr>
      <td>데이터베이스</td>
      <td>김교수</td>
      <td>DB 책2</td>
    </tr>
    <tr>
      <td>데이터베이스</td>
      <td>백교수</td>
      <td>DB 책1</td>
    </tr>
    <tr>
      <td>데이터베이스</td>
      <td>백교수</td>
      <td>DB 책2</td>
    </tr>
    <tr>
      <td>자료구조</td>
      <td>정교수</td>
      <td>DATA STRUCTURE 1</td>
    </tr>
    <tr>
      <td>자료구조</td>
      <td>정교수</td>
      <td>DATA STRUCTURE 2</td>
    </tr>
    <tr>
      <td>자료구조</td>
      <td>정교수</td>
      <td>DATA STRUCTURE 3</td>
    </tr>
  </tbody>
</table>


* 5NF 만족하는 릴레이션
*
  * 과목 교수
<table>
  <tbody>
    <tr>
      <th>과목</th>
      <th>교수</th>
    </tr>
    <tr>
      <td>데이터베이스</td>
      <td>김교수</td>
    </tr>
    <tr>
      <td>데이터베이스</td>
      <td>백교수</td>
    </tr>
    <tr>
      <td>자료구조</td>
      <td>정교수</td>
    </tr>
  </tbody>
</table>

*
  * 교수 교재
<table>
  <tbody>
    <tr>
      <th>교수</th>
      <th>교재</th>
    </tr>
    <tr>
      <td>김교수</td>
      <td>DB 책1</td>
    </tr>
    <tr>
      <td>김교수</td>
      <td>DB 책2</td>
    </tr>
    <tr>
      <td>백교수</td>
      <td>DB 책1</td>
    </tr>
    <tr>
      <td>백교수</td>
      <td>DB 책2</td>
    </tr>
    <tr>
      <td>정교수</td>
      <td>DATA STRUCTURE 1</td>
    </tr>
    <tr>
      <td>정교수</td>
      <td>DATA STRUCTURE 2</td>
    </tr>
    <tr>
      <td>정교수</td>
      <td>DATA STRUCTURE 3</td>
    </tr>
  </tbody>
</table>

*
  * 과목 교재
<table>
  <tbody>
    <tr>
      <th>과목</th>
      <th>교재</th>
    </tr>
    <tr>
      <td>데이터베이스</td>
      <td>DB 책1</td>
    </tr>
    <tr>
      <td>데이터베이스</td>
      <td>DB 책2</td>
    </tr>
    <tr>
      <td>자료구조</td>
      <td>DATA STRUCTURE 1</td>
    </tr>
    <tr>
      <td>자료구조</td>
      <td>DATA STRUCTURE 2</td>
    </tr>
    <tr>
      <td>자료구조</td>
      <td>DATA STRUCTURE 3</td>
    </tr>
  </tbody>
</table>





<br/><br/><br/>

[참고1](https://itwiki.kr/w/%EB%8D%B0%EC%9D%B4%ED%84%B0%EB%B2%A0%EC%9D%B4%EC%8A%A4_%EC%A0%95%EA%B7%9C%ED%99%94)  
[참고2](https://mangkyu.tistory.com/110)
[참고3](https://minimax95.tistory.com/50)