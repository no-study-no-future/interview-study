# KMP 알고리즘

## 💡문자열 매칭 알고리즘이란?

특정한 글이 있을 때 그 글 안에서 하나의 문자열을 찾는 알고리즘이다. 노트패드나 워드파일 또는 웹 브라우저 데이터베이스에서 문자열을 검색할 때 패턴 매칭 알고리즘을 사용하여 검색 결과를 표시한다. 

문자열 매칭 알고리즘 종류로는 다음과 같다.

1. Naive Matching (원시적인 매칭)
2. Rabin-Karp Algorithm (라빈-카프 알고리즘)
3. Boyer-Moore Algorithm (보이어-무어 알고리즘)
4. Knuth-Morris-Pratt(KMP) Algorithm (KMP알고리즘)

이 중 Naive 매칭과 KMP 알고리즘에 대해서 알아보자.

## Naive Matching (원시적인 매칭)

**Brute Force 방식**을 이용한 매칭으로, 문자열을 **처음부터 끝까지 차례대로 순회**하면서 패턴 내의 **문자들을 일일이 비교**하는 방식으로 동작한다.

최악의 경우, 텍스트의 모든 위치에서 패턴을 비교해야 한다. 이때 시간 복잡도는 ***O(mn)***

```java
p[] : 찾을 패턴,		t[] : 전체 텍스트
M : 찾을 패턴의 길이,		N : 전체 텍스트의 길이

BruteForce(p[], t[])
	i = 0, j = 0
	WHILE j < M AND i < N
		IF t[i] ≠ p[j]
			i = i - j
			j = -1
		i = i + 1
		j = j + 1
    
	IF j == M : RETURN i - M
	ELSE	  : RETURN i
```

효율적이지는 않지만 코드가 직관적이라서 간단하게 사용하기 좋다.

## Knuth-Morris-Pratt(KMP) Algorithm (KMP알고리즘)

접두사와 접미사의 개념을 활용하여 **반복되는 연산을 얼마나 줄일 수 있는지를 판별**하여 매칭할 문자열을 빠르게 점프하는 기법이다. 불일치가 발생한 텍스트 문자열의 앞 부분(=접두사)에 어떤 문자가 있는지를 미리 알고있으므로, 불일치가 발생한 앞 부분에 다시 비교하지 않고 매칭을 수행한다.

KMP 알고리즘을 동작시키기 위해서는 접두사도 되고 접미사도 되는 **문자열의 최대 길이를 저장해주는 pi**을 미리 정의해줘야한다. pi[]은 pattern이 어디까지 일치했는지가 주어질 때 다음 시작 위치를 어디로 해야할지를 말해주기 때문에, 이를 흔히 **부분 일치 테이블**이라고 부른다.

> pi[k] : 처음부터 k 인덱스까지를 끝으로 하는 문자열에서 일치하는 접두사와 접미사가 일치하는 최대 길이

![부분일치테이블](img/%EB%B6%80%EB%B6%84%EC%9D%BC%EC%B9%98%ED%85%8C%EC%9D%B4%EB%B8%94.png)

### 부분일치 테이블 배열 만들기

```java
private static int[] makeTable(char[] pattern, int size) {
    int[] pi = new int[size];

    int idx = 0;
    for (int i = 1; i < size; i++) {
        // 일치하는 문자가 발생했을 대(idx > 0), 연속적으로 더 일치하지 않으면 idx = table[idx-1]로 돌려준다
        while (idx > 0 && pattern[i] != pattern[idx]) {
            idx = pi[idx-1];
        }

        // 일치하는 경우
        if (pattern[i] == pattern[idx]) {
            // i길이 문자열의 공통 길이는 j의 위치 + 1 
            idx += 1;
            pi[i] = idx;
        }
    }

    return pi;
}
```

![부분일치테이블배열만들기](img/%EB%B6%80%EB%B6%84%EC%9D%BC%EC%B9%98%ED%85%8C%EC%9D%B4%EB%B8%94%EB%A7%8C%EB%93%A4%EA%B8%B01.png)

![부분일치테이블배열만들기](img/%EB%B6%80%EB%B6%84%EC%9D%BC%EC%B9%98%ED%85%8C%EC%9D%B4%EB%B8%94%EB%A7%8C%EB%93%A4%EA%B8%B02.png)

![부분일치테이블배열만들기](img/%EB%B6%80%EB%B6%84%EC%9D%BC%EC%B9%98%ED%85%8C%EC%9D%B4%EB%B8%94%EB%A7%8C%EB%93%A4%EA%B8%B03.png)

![부분일치테이블배열만들기](img/%EB%B6%80%EB%B6%84%EC%9D%BC%EC%B9%98%ED%85%8C%EC%9D%B4%EB%B8%94%EB%A7%8C%EB%93%A4%EA%B8%B04.png)

![부분일치테이블배열만들기](img/%EB%B6%80%EB%B6%84%EC%9D%BC%EC%B9%98%ED%85%8C%EC%9D%B4%EB%B8%94%EB%A7%8C%EB%93%A4%EA%B8%B05.png)

![부분일치테이블배열만들기](img/%EB%B6%80%EB%B6%84%EC%9D%BC%EC%B9%98%ED%85%8C%EC%9D%B4%EB%B8%94%EB%A7%8C%EB%93%A4%EA%B8%B06.png)

![부분일치테이블배열만들기](img/%EB%B6%80%EB%B6%84%EC%9D%BC%EC%B9%98%ED%85%8C%EC%9D%B4%EB%B8%94%EB%A7%8C%EB%93%A4%EA%B8%B07.png)

![부분일치테이블배열만들기](img/%EB%B6%80%EB%B6%84%EC%9D%BC%EC%B9%98%ED%85%8C%EC%9D%B4%EB%B8%94%EB%A7%8C%EB%93%A4%EA%B8%B08.png)

![부분일치테이블배열만들기](img/%EB%B6%80%EB%B6%84%EC%9D%BC%EC%B9%98%ED%85%8C%EC%9D%B4%EB%B8%94%EB%A7%8C%EB%93%A4%EA%B8%B09.png)

### ⭐KMP 알고리즘 구현

전통적인 구현은 이해가 조금 까다롭기 때문에 간결하면서 자주 사용되는 알고리즘을 살펴본다.

시간복잡도는 table생성할 때 반복문 한번, kmp 매칭할 때 한번 해서 ***O(M+N)*** 이다.

```java
private static void KMP(char[] search, char[] pattern, int s_size, int p_size) {
    int[] table = makeTable(pattern, p_size);

    int idx = 0; // 현재 대응되는 글자 수

    for (int i = 0; i < s_size; i++) {
        // idx번 글자와 짚더미의 해당 글자가 불일치할 경우
        // 현재 대응된 글자의 수를 table[idx-1]번으로 줄인다. (다시 매칭을 시작해야하므로)
        while(idx > 0 && search[i] != pattern[idx]) {
            idx = table[idx-1];
        }

        // 글자가 대응할 경우
        if (search[i] == pattern[idx]) {
            // 문자열 매칭 성공
            if (idx == p_size-1) {
                idx = table[idx]; // 계속 탐색하기 위해 idx를 돌리기
                System.out.println(i-idx+1 + "번째에서 찾았습니다. ~" + (i+1) );
            }
            // 다음 문자 매칭을 위한 증가
            else idx += 1;
        }
    }
}
```

### KMP 알고리즘 탐색과정

> 검색할 문자열(search): `abacababaca` <br>찾는 문자열(pattern): `ababaca` <br>부분일치 테이블(table): `[0 0 1 2 3 0 1]`

1) i = 0, idx = 0 → idx 1증가

   a 일치

   | search  | a    | b    | a    | c    | a    | b    | a    | b    | a    | c    | a    |
   | ------- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- |
   | pattern | a    | b    | a    | b    | a    | c    | a    |      |      |      |      |
   | idx     | 0    |      |      |      |      |      |      |      |      |      |      |

2. i = 1, idx = 1 → idx 1증가

   b 일치

   | search  | a    | b    | a    | c    | a    | b    | a    | b    | a    | c    | a    |
   | ------- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- |
   | pattern | a    | b    | a    | b    | a    | c    | a    |      |      |      |      |
   | idx     | 0    | 1    |      |      |      |      |      |      |      |      |      |

3. i = 2, idx = 2 → idx 1증가

   a 일치

   | search  | a    | b    | a    | c    | a    | b    | a    | b    | a    | c    | a    |
   | ------- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- |
   | pattern | a    | b    | a    | b    | a    | c    | a    |      |      |      |      |
   | idx     | 0    | 1    | 2    |      |      |      |      |      |      |      |      |

4. i = 3, idx = 3 → idx = table[idx-1] = table[2] = 1 → idx = table[idx-1] = table[0] = 0

   b != c 불일치

   > 접두사 접미사가 일치하지 않으므로 idx=0부터 재탐색 시작한다.

   | search  | a    | b    | a    | c    | a    | b    | a    | b    | a    | c    | a    |
   | ------- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- |
   | pattern | a    | b    | a    | b    | a    | c    | a    |      |      |      |      |
   | idx     | 0    | 1    | 2    | 1    |      |      |      |      |      |      |      |

5. i = 4, idx = 0 → idx 증가

   a 일치

   | search  | a    | b    | a    | c    | a    | b    | a    | b    | a    | c    | a    |
   | ------- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- |
   | pattern |      |      |      |      | a    | b    | a    | b    | a    | c    | a    |
   | idx     |      |      |      |      | 0    |      |      |      |      |      |      |

8. i = 5, idx = 1 → idx 증가

   | search  | a    | b    | a    | c    | a    | b    | a    | b    | a    | c    | a    |
   | ------- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- |
   | pattern |      |      |      |      | a    | b    | a    | b    | a    | c    | a    |
   | idx     |      |      |      |      | 0    | 1    |      |      |      |      |      |

9. i = 6 ~ 10

   idx = 6 (pattern 문자열 길이 - 1)에 도달하면서 매칭이 되었으므로 탐색 종료

   > 만약 계속 탐색하려면 idx = table[idx]를 해줘서 idx를 다시 돌려주면 된다.

   | search  | a    | b    | a    | c    | a    | b    | a    | b    | a    | c    | a    |
   | ------- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- |
   | pattern |      |      |      |      | a    | b    | a    | b    | a    | c    | a    |
   | idx     |      |      |      |      | 0    | 1    | 2    | 3    | 4    | 5    | 6    |

## Ref

- SSAFY Java 강의
- https://loosie.tistory.com/192
