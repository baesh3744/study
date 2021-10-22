# 트리의 지름 (Diameter of tree)

## 트리의 지름이란?

트리에서 어떤 두 노드를 선택해서 양쪽으로 쫙 당길 때, 가장 길게 늘어나는 경우가 있을 것이다. 이때 트리의 모든 노드들은 이 두 노드를 지름의 끝 점으로 하는 원 안에 들어가게 된다. 이런 두 노드 사이의 경로의 길이를 트리의 지름이라고 한다.

<br>

## 트리의 지름을 구하는 과정

1. 트리에서 임의의 노드 x를 선택한다.

2. x에서 가장 먼 노드 y를 찾는다.

3. y에서 가장 먼 노드 z를 찾는다.

4. y와 z 사이의 거리가 트리의 지름이다.

<br>

## Python code

```python
from collections import deque


v: int  # 노드의 개수
graph: list[list[int]]  # 트리, graph[i] = 노드i와 연결된 (노드, 거리)


# start 노드에서 가장 먼 노드를 찾는다.
# BFS로 구현되어 있지만, DFS로 구현하여도 무관하다.
def get_farthest(start: int) -> tuple[int, int]:
    max_: tuple[int, int] = (-1, -1)
    distances = [-1] * v
    que: deque[int] = deque()

    que.append(start)
    distances[start] = 0

    while que:
        cur_node = que.popleft()
        for next_node, dist in graph[cur_node]:
            if distances[next_node] != -1:
                continue
            distances[next_node] = distances[cur_node] + dist
            que.append(next_node)
            if max_[1] < distances[next_node]:
                max_ = next_node, distances[next_node]
    return max_


def get_diameter_of_tree() -> int:
    node, _ = get_farthest(0)
    _, dist = get_farthest(node)
    return dist
```

<br>

## Reference

https://www.acmicpc.net/problem/1967

https://velog.io/@coding_egg/%EB%B0%B1%EC%A4%80-1991%EB%B2%88-%ED%8A%B8%EB%A6%AC%EC%9D%98-%EC%A7%80%EB%A6%84-python-%ED%8C%8C%EC%9D%B4%EC%8D%AC
