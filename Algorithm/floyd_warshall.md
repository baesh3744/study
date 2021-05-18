# 플로이드-와샬(Floyd-Warshall)

## 플로이드-와샬 알고리즘이란?

---

-   모든 정점 사이의 최단 경로를 찾는 알고리즘이다.

-   3중 for문을 통해 구현되며, 시간 복잡도는 O(N^3)이다.

<br>

<br>

## 플로이드-와샬 알고리즘 과정

---

1. 최단 경로를 저장하는 배열 arr을 생성한다.

    - arr[i][j]: i -> j로 이동할 때, 최단 경로

    - 하나의 정점에서 다른 정점으로 바로 갈 수 있으면 그 비용으로, 갈 수 없으면 inf로 초기화한다.

    - 자기 자신으로의 이동 즉, arr[i][i]는 0으로 초기화한다.

2. 3중 for문을 통해 거쳐가는 정점을 설정하고, 해당 정점을 거쳐갈 때 비용이 줄어드는 경우에 배열의 값을 바꿔준다.

<br>

<br>

## 플로이드-와샬 알고리즘 예시

---

<img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FmMV7e%2FbtqRcIuiDlv%2FYejbMLARvL6cEBQ7vTs4Y1%2Fimg.png" width="300">

[이미지 출처] https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FmMV7e%2FbtqRcIuiDlv%2FYejbMLARvL6cEBQ7vTs4Y1%2Fimg.png

1. 초기 배열

    |     |     |     |     |
    | --- | --- | --- | --- |
    | 0   | 3   | inf | 6   |
    | 4   | 0   | 7   | inf |
    | 5   | inf | 0   | 4   |
    | inf | inf | 2   | 0   |
    |     |     |     |     |

2. 1을 거쳐갈 때

    |     |     |     |     |
    | --- | --- | --- | --- |
    | 0   | 3   | inf | 6   |
    | 4   | 0   | 7   | 10  |
    | 5   | 8   | 0   | 4   |
    | inf | inf | 2   | 0   |
    |     |     |     |     |

3. 2를 거쳐갈 때

    |     |     |     |     |
    | --- | --- | --- | --- |
    | 0   | 3   | 10  | 6   |
    | 4   | 0   | 7   | 10  |
    | 5   | 8   | 0   | 4   |
    | inf | inf | 2   | 0   |
    |     |     |     |     |

4. 3을 거쳐갈 때

    |     |     |     |     |
    | --- | --- | --- | --- |
    | 0   | 3   | 10  | 6   |
    | 4   | 0   | 7   | 10  |
    | 5   | 8   | 0   | 4   |
    | 7   | 10  | 2   | 0   |
    |     |     |     |     |

5. 4를 거쳐갈 때

    |     |     |     |     |
    | --- | --- | --- | --- |
    | 0   | 3   | 10  | 6   |
    | 4   | 0   | 7   | 10  |
    | 5   | 8   | 0   | 4   |
    | 7   | 10  | 2   | 0   |
    |     |     |     |     |

<br>

<br>

## Python Code

---

```python
import sys

# inputs: [start, end, distance]의 배열
def floyd_warshall(n: int, inputs: list[list[int]]) -> list[list[int]]:
    distances: list[list[int]] = [[sys.maxsize for _ in range(n)] for _ in range(n)]

    # 최단 경로를 저장하는 배열 초기화
    for start, end, distance in inputs:
        distances[start][end] = distance

    for through in range(n):  # 거쳐가는 정점
        for start in range(n):  # 시작 정점
            for end in range(n):  # 도착 정점
                distances[start][end] = min(distances[start][end],
                                            distances[start][through] + distances[through][end])

    return distances
```

<br>

<br>

## References

---

https://bgspro.tistory.com/38
