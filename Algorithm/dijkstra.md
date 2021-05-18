# 다익스트라(Dijkstra)

## 다익스트라 알고리즘이란?

---

-   하나의 시작 정점으로부터 다른 모든 정점까지의 최단 경로를 찾는 최단 경로 알고리즘

    -   A, B, C, D가 존재할 때, A를 시작 정점이라 한다면, A - B, A - C, A - D 중 가장 짧은 경로를 찾는다.

-   첫 정점을 기준으로 연결되어 있는 정점들을 추가해가며, 최단 거리를 갱신한다.

-   BFS와 유사하다.

<br>

<br>

## 다익스트라 알고리즘 과정

---

1.  초기화

    -   최단 거리를 저장하는 배열을 선언하고, 첫 정점의 거리는 0, 나머지는 무한대로 저장한다.

    -   우선순위 큐에 (첫 정점, 거리 0)을 넣는다.

2.  최단 거리 업데이트

    2-1. 우선순위 큐에서 정점 한 개(U)를 꺼낸다.

    2-2. 시작 정점과 U 사이의 거리가 배열에 저장된 최단 거리보다 크면, 2-1로 돌아간다. 즉, 무시한다.

    2-3. U와 인접한 정점(V)과 시작 정점 사이의 거리를 계산한다.

    -   계산한 거리가 배열에 저장된 값보다 작으면, 배열을 업데이트하고 우선순위 큐에 (V, 거리)를 넣는다.

    -   계산한 거리가 배열에 저장된 값보다 크면, 무시한다.

    2-4. 우선순위 큐가 빌 때까지 2-1 ~ 2-3의 과정을 반복한다.

<br>

<br>

## 다익스트라 알고리즘 예시

---

![https://www.bogotobogo.com/python/images/Dijkstra/graph_diagram.png](https://www.bogotobogo.com/python/images/Dijkstra/graph_diagram.png)

[이미지 출처] https://www.bogotobogo.com/python/images/Dijkstra/graph_diagram.png

1. 초기화

    최단 거리 저장 배열
    | | | | | | |
    |---|-----|-----|-----|-----|-----|
    | a | b | c | d | e | f |
    | 0 | inf | inf | inf | inf | inf |
    | | | | | | |

    우선순위 큐
    | |
    |--------|
    | (0, a) |
    | |

2. 우선순위 큐에서 (0, a)을 꺼낸다. a와 연결된 정점 b, c, f와의 거리가 모두 inf보다 작으므로 최단 거리 저장 배열을 업데이트하고 우선순위 큐에 (7, b), (9, c), (14, f)를 넣는다.

    최단 거리 저장 배열
    | | | | | | |
    |---|-----|-----|-----|-----|-----|
    | a | b | c | d | e | f |
    | 0 | 7 | 9 | inf | inf | 14 |
    | | | | | | |

    우선순위 큐
    | | | |
    |--------|--------|---------|
    | (7, b) | (9, c) | (14, f) |
    | | | |

3. 우선순위 큐에서 (7, b)를 꺼낸다. b와 연결된 정점 a, c, d와 시작 정점 간의 거리를 계산한다.

    - 시작 정점 - a: 7 + 7 = 14 --> 배열에 저장된 값 0보다 크므로, 무시한다.

    - 시작 정점 - c: 7 + 10 = 17 --> 배열에 저장된 값 9보다 크므로, 무시한다.

    - 시작 정점 - d: 7 + 15 = 23 --> 배열에 저장된 값 inf보다 크므로, 배열을 업데이트하고 우선순위 큐에 (23, d)를 넣는다.

    최단 거리 저장 배열
    | | | | | | |
    |---|-----|-----|-----|-----|-----|
    | a | b | c | d | e | f |
    | 0 | 7 | 9 | 23 | inf | 14 |
    | | | | | | |

    우선순위 큐
    | | | |
    |--------|---------|---------|
    | (9, c) | (14, f) | (23, d) |
    | | | |

4. 우선순위 큐에서 (9, c)를 꺼낸다. c와 연결된 정점 a, b, d, f와 시작 정점 간의 거리를 계산한다.

    - 시작 정점 - a: 9 + 9 = 18 --> 배열에 저장된 값 0보다 크므로, 무시한다.

    - 시작 정점 - b: 9 + 10 = 19 --> 배열에 저장된 값 7보다 크므로, 무시한다.

    - 시작 정점 - d: 9 + 11 = 20 --> 배열에 저장된 값 23보다 작으므로, 배열을 업데이트하고 우선순위 큐에 (20, d)를 넣는다.

    - 시작 정점 - f: 9 + 2 = 11 --> 배열에 저장된 값 14보다 작으므로, 배열을 업데이트하고 우선순위 큐에 (11, f)를 넣는다.

    최단 거리 저장 배열
    | | | | | | |
    |---|-----|-----|-----|-----|-----|
    | a | b | c | d | e | f |
    | 0 | 7 | 9 | 23 | inf | 11 |
    | | | | | | |

    우선순위 큐
    | | | | |
    |---------|---------|---------|---------|
    | (11, f) | (14, f) | (20, d) | (23, d) |
    | | | | |

5. 우선순위 큐에서 (11, f)를 꺼내고, 위의 작업을 반복한다.

6. 우선순위 큐에서 (14, f)를 꺼낸다. 큐에서 꺼낸 값 14가 배열에 저장된 값 11보다 크므로 무시한다.

7. 큐가 빌 때가지 반복한다.

<br>

<br>

## Python Code

---

```python
# graph = {
#     'A': { 'B': 7, 'C': 9, 'F': 14 },
#     'B': { 'A': 7, 'C': 10, 'D': 15 },
#     'C': { 'A': 9, 'B': 10, 'D': 11, 'F': 2 },
#     'D': { 'B': 15, 'C': 11, 'E': 6 },
#     'E': { 'D': 6, 'F': 9 },
#     'F': { 'A': 14, 'C': 2, 'E': 9 }
# }

import heapq

def dijkstra(graph: dict[str, dict[str, int]], start: str) -> dict[str, int]:
    distances: dict[str, int] = {node: float('inf') for node in graph}
    que: list[list[int]] = []

    distances[start] = 0
    heapq.heappush(que, [distances[start], start])

    while que:
        cur_distance, cur_node = heapq.heappop(que)

        if distances[cur_node] < cur_distance:
            continue

        for adj_node, adj_distance in graph[cur_node].items():
            distance: int = cur_distance + adj_distance

            if distance < distances[adj_node]:
                distances[adj_node] = distance
                heapq.heappush(que, [distance, adj_node])

    return distances
```

<br>

<br>

## References

---

https://www.fun-coding.org/Chapter20-shortest-live.html
