# Binary search
작성일 : 2022.03.20

---
## 정의
이분 탐색, 이진 탐색, 이진 검색 등으로 불리는 이 알고리즘은 **오름 차순으로 정렬된 리스트 혹은 배열**에서 특정한 값의 위치를 찾는데 사용되는 검색 알고리즘의 한 종류입니다.   
이 알고리즘은 탐색 범위를 1/2씩 줄여나가는 방식이기 때문에 "Binary"가 붙여졌습니다.

## 탐색 과정
1. 리스트 혹은 배열 중앙의 임의의 값을 선택합니다.
2. 선택한 값과 목표로 하는 값을 비교합니다.
3. 목표의 값이 중앙 값 보다 작다면 중앙값이 데이터 집합의 마지막 값이 됩니다. / 크다면 중앙값 + 1이 데이터 집합의 시작 값이 됩니다.
4. 찾고자 하는 결과에 도달할 때 까지 1-3을 반복합니다.

## 성능
시간 복잡도 : **logN**

## 구현
**Java**
```java
/**
*   lst : 오름차순 정렬 된 배열
*   val : 찾고자 하는 목표 값
*/
public int binarySearch(List<Integer> lst, final int val) {
    int left = 0, right = lst.size(), mid;
    while(left<=right) {
        mid = (left+right)/2;
        int midVal = lst.get(mid);
        if(val == midVal) {
            return mid;
        }

        if(midVal < val) {
            left = mid+1;
        } else {
            right = mid;
        }
    }
}
```
- 주어진 상황에 맞춰서 `binarySearch` 내부의 조건식을 변경해주면 됩니다.

**C++**   
\<algorithm\> 헤더에 관련 함수가 존재합니다.
- upper_bound : 목표 값을 초과하는 첫 요소 `iterator` 반환
- lower_bound : 목표 값 이상인 첫 요소 `iterator` 반환
- binary_search : 목표 값과 일치하는 요소가 존재하는지 `bool` 반환