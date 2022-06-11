# 최대 유량 알고리즘(Maximum Flow Algorithm)

## 최대 유량 알고리즘

시작점부터 도착점까지 흐를 수 있는 최대 유량을 구하는 알고리즘입니다.

### 용어

-   유량 네트워크(flow network) - 간선에 용량이라는 속성이 있는 그래프
-   소스(source) - 유량이 시작하는 정점
-   싱크(sink) - 유량이 도착해야 하는 정점
-   용량(capacity) - 각 간선에 흐를 수 있는 최대 유량
-   유량(flow) - 각 간선에 대해서 다음 조건을 만족하는 값
    -   용량의 제한(capacity constraint) - 유량은 그 간선의 용량을 초과할 수 없습니다.
    -   유량의 보존(conservation of flows) - 소스와 싱크를 제외한 정점에는 들어오는 유량과 나가는 유량이 같습니다.
-   유량의 대칭성(skew symmetry) - u -> v로 f만큼의 유량이 흐르면, v -> u로 -f만큼의 유량이 흐른다고 생각합니다.
-   value of flow - 소스에서 싱크로 흘러가는 유량의 크기입니다. 소스에서 나오는 유량은 모두 싱크로 흘러가므로 소스에서 나오는 유량의 합과 같습니다.
-   최대 유량 문제(maximum flow problem) - value of flow의 최댓값을 구하는 문제이며, 이를 구하는 알고리즘을 최대 유량 알고리즘이라고 합니다.

<br>

## 포드-풀커슨 알고리즘(Ford-Fulkerson Algorithm)

증가 경로(augmenting path)를 찾고 흘려보내는 것을 반복합니다.

증가 경로를 찾는 방법은 명시하지 않았기 때문에 포드-풀커슨 알고리즘이 아니라 포드-풀커슨 방법론이라고 불리기도 합니다.

### 동작

1. 소스에서 싱크까지 모든 간선의 잔여 용량이 0보다 큰 증가 경로 p를 구합니다. 없으면 종료합니다.
2. 경로 p에서 최소 용량 c를 구합니다.
3. 경로 p에 포함된 모든 간선 (u, v)의 유량에 c를 더하고, 반대 간선 (v, u)의 유량에 c를 뺍니다.
4. 1~3을 반복합니다.

### 시간복잡도

최대 유량 f에 대해서 증가 경로를 찾기위해 탐색을 최대 f번 하므로 f \* O(|V|+|E|) = O(f|E|)입니다.

<br>

## 에드몬드-카프 알고리즘(Edmonds-Karp Algorithm)

포드-풀커슨 알고리즘에서 증가 경로를 찾을 때 BFS를 쓰면 O(VE^2) 안에 찾을 수 있다고 알려져 있습니다. 포드-풀커슨 알고리즘에서 BFS를 쓴 최대 유량 알고리즘을 에드몬드-카프 알고리즘이라고 합니다.

<br>

## Python Code

```python
import sys
from collections import deque


MAX = sys.maxsize

n: int  # 노드의 개수
capacity_network: list[list[int]]
flow_network: list[list[int]]
adj_list: list[list[int]]


def find_flow(start: int, end: int, prev_list: list[int]) -> int:
    flow: int = MAX
    cur: int = end
    while cur != start:
        prev: int = prev_list[cur]
        flow = min(flow, capacity_network[prev][cur] - flow[prev][cur])
        cur = prev
    return flow


def update_network(start: int, end: int, flow: int, prev_list: list[int]) -> None:
    cur: int = end
    while cur != start:
        prev: int = prev_list[cur]
        flow_network[prev][cur] += flow
        flow_network[cur][prev] -= flow
        cur = prev


def bfs(start: int, end: int) -> int:
    prev_list: list[int] = [-1 for _ in range(n)]
    queue: deque[int] = deque([start])
    while queue:
        node = queue.popleft()
        for adj_node in adj_list[node]:
            if (
                prev_list[adj_node] == -1
                and capacity_network[node][adj_node] - flow_network[node][adj_node] > 0
            ):
                queue.append(adj_node)
                prev_list[adj_node] = node
                if adj_node == end:
                    flow = find_flow(start, end, prev_list)
                    update_network(start, end, flow, prev_list)
                    return flow
    return 0


def edmonds_karp(start: int, end: int) -> int:
    max_flow: int = 0
    while True:
        flow = bfs(start, end)
        if flow == 0:
            break
        max_flow += flow
    return max_flow
```

<br>

## Reference

https://iknoom.tistory.com/13

https://hyeyoo.com/39
