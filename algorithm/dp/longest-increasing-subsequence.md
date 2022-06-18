# 최장 증가 부분수열 (LIS, Longest Increasing Subsequence)

## DP를 이용한 방법 - O(N^2)

- cache[i]: i번째 원소를 마지막으로 하는 LIS의 길이
- cache[i]는 0 ~ i - 1까지의 원소들에서 i번째 원소보다 값이 작은 것들 중에서 가장 큰 cache값 + 1이다.

```python
def get_lis_length_by_dp(n: int, seq: list[int]) -> int:
    cache: list[int] = [-1 for _ in range(n)]
    max_length: int = 0

    for idx in range(n):
        length: int = 0

        for jdx in range(idx):
            if seq[jdx] < seq[idx]:
                length = max(length, cache[jdx])

        cache[idx] = length + 1
        max_length = max(max_length, cache[idx])

    return max_length
```

<br>

## 이분탐색을 이용한 방법 - O(NlogN)

- 이 알고리즘은 다음과 같은 리스트 L을 업데이트하며 동작한다.

  - L[i]: 길이가 i인 증가하는 부분수열을 만들 수 있는 마지막 원소 중 가장 작은 값

  1. L이 비어있거나, 현재 L의 마지막 원소보다 현재 참조하는 값 num이 더 큰 경우 L의 뒤에 num을 추가한다.

  2. 그렇지 않은 경우 L에서 num의 lower bound를 찾아서, 그 자리의 값을 num으로 바꾼다.

- 정렬된 배열 arr에서 어떤 값 val의 lower bound란 arr을 정렬된 상태로 유지하면서 val이 삽입될 수 있는 위치들 중에서 가장 작은 인덱스를 말한다.

- 이 알고리즘은 각 원소를 참조하는 시간 N과 각 원소의 lower bound를 찾는 시간 logN이 소요된다.

- L 업데이트 과정
  | | | | | | | |
  | --- | --- | --- | --- | --- | --- | --- |
  | 3 | 2 | 5 | 2 | 3 | 1 | 4 |
  | | | | | | | |
  - 3 -> L이 비어있기 때문에, 3을 추가한다.
    | |
    |---|
    | 3 |
    | |
  - 2 -> 2가 L의 마지막 원소 3보다 작다. 2의 lower bound는 0이기 때문에, 인덱스 0의 값을 2로 바꾼다.
    | |
    |---|
    | 2 |
    | |
  - 5 -> 5가 L의 마지막 원소인 2보다 크기 때문에, L에 5를 추가한다.
    | | |
    |---|---|
    | 2 | 5 |
    | | |
  - 2 -> 2의 lower bound는 0이기 때문에, 인덱스 0의 값을 2로 바꾼다.
    | | |
    |---|---|
    | 2 | 5 |
    | | |
  - 3 -> 3의 lower bound는 1이기 때문에, 인덱스 1의 값을 3으로 바꾼다.
    | | |
    |---|---|
    | 2 | 3 |
    | | |
  - 1 -> 1의 lower bound는 0이기 때문에, 인덱스 0의 값을 1로 바꾼다.
    | | |
    |---|---|
    | 1 | 3 |
    | | |
  - 4 -> 4가 L의 마지막 원소 3보다 크기 때문에, L에 4를 추가한다.
    | | | |
    |---|---|---|
    | 1 | 3 | 4 |
    | | | |

<br>

### 이분 탐색을 직접 구현한 방법

```python
def get_lis_length_by_binary_search(n: int, seq: list[int]) -> int:
    lis: list[int] = []

    for num in seq:
        start: int = 0
        end: int = len(lis) - 1

        while start <= end:
            mid: int = (start + end) // 2
            if lis[mid] < num:
                start = mid + 1
            else:
                end = mid - 1

        if len(lis) <= start:
            lis.append(num)
        else:
            lis[start] = num

    return len(lis)
```

<br>

### bisect을 이용한 방법 (Python)

- lower bound 계산 과정을 bisect.bisect_left() 함수로 구한다.

```python
from bisect import bisect_left

def get_lis_length_by_bisect(seq: list[int]) -> int:
    lis: list[int] = []

    for num in seq:
        if len(lis) == 0 or lis[-1] < num:
            lis.append(num)
        else:
            lis[bisect_left(lis, num)] = num

    return len(lis)
```

<br>

## LIS를 구하는 방법

```python
def get_lis(n: int, nums: list[int]) -> list[int]:
    # 위의 3가지 방법 중 한 가지를 이용하여
    #   lis_length: lis의 길이
    #   lis_indexes: nums의 각각의 요소들이 lis의 어느 index에 들어갈 수 있는지 저장한 리스트
    # 구한다.
    lis_length, lis_indexes = get_lis_length(nums)

    lis: list[int] = [-1 for _ in range(lis_length)]
    idx: int = lis_length - 1
    for pointer in range(n - 1, -1, -1):
        if lis_indexes[pointer] == idx:
            lis[idx] = nums[pointer]
            idx -= 1

    return lis
```

<br>

## Reference

https://namhandong.tistory.com/135

https://seungkwan.tistory.com/8
