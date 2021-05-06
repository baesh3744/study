# bisect

## bisect - 배열 이진 분할 알고리즘

---

-   bisect 모듈은 정렬된 리스트에 값을 삽입한 후에 다시 정렬할 필요가 없도록 관리해준다.

-   기본적인 이진 분할 알고리즘을 사용하기 때문에 bisect이라고 불린다.

<br>

### bisect.bisect_left(a, x, lo=0, hi=len(a))

---

-   정렬된 순서를 유지하도록 a에 x를 삽입할 위치를 찾는다.

-   x가 a에 이미 있으면, 삽입 위치는 기존 항목 앞(왼쪽)이 된다.

<br>

### bisect.bisect_right(a, x, lo=0, hi=len(a))

### bisect.bisect(a, x, lo=0, hi=len(a))

---

-   bisect_left()와 비슷하지만, x가 a에 있으면, 삽입 위치는 기존 항목 뒤(오른쪽)가 된다.
