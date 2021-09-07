# 위상 정렬 (Topology Sort)

## 위상 정렬이란?

-   위상 정렬은 **순서가 정해져있는 작업**을 차례로 수행해야 할 때, 그 순서를 결정해주는 알고리즘이다.

-   위상 정렬은 **DAG(Directed Acyclic Graph)** 에만 적용이 가능하다. DAG는 사이클이 발생하지 않는 방향 그래프를 의미한다.

-   위상 정렬 알고리즘은 2가지 결과를 낸다. **(1)** 현재 그래프가 위상 정렬이 가능한지 **(2)** 위상 정렬이 가능하다면 그 결과가 무엇인지이다.

-   위상 정렬을 수행하는 알고리즘은 두 가지가 존재한다. **스택(Stack)** 을 이용한 방식과 **큐(Queue)** 를 이용한 방식이다.

<br>

## 위상 정렬 알고리즘 (with Queue)

1. 진입차수가 0인 정점을 큐에 삽입한다. 진입차수란 한 정점으로 들어오는 간선의 개수를 말한다.

2. 큐가 빌 때까지 다음의 과정을 반복한다.

    2-1. 큐에서 노드를 꺼내 연결된 모든 간선을 제거한다.

    2-2. 간선 제거 이후, 진입차수가 0이 된 노드를 큐에 삽입한다.

3. 모든 노드를 방문하기 전에 큐가 빈다면 사이클이 존재하는 것이고, 모든 노드를 방문했다면 큐에서 꺼낸 순서가 위상 정렬의 결과이다.

![https://gmlwjd9405.github.io/images/algorithm-topological-sort/topological-sort-example.png
](https://gmlwjd9405.github.io/images/algorithm-topological-sort/topological-sort-example.png)
[이미지 출처] https://gmlwjd9405.github.io/2018/08/27/algorithm-topological-sort.html

<br>

## Python Code

```python
from collections import deque


indegrees: list[int] = [ ... ]  # 노드에 대한 진입차수를 담은 리스트
graph: list[list[int]] = [[ ... ]]  # graph[a] = b는 a -> b의 간선을 의미
result: list[int] = []  # 알고리즘 수행 결과를 담을 리스트


def topology_sort() -> bool:
    que: deque[int] = deque()

    # 1. 진입차수가 0인 노드를 큐에 삽입
    for idx in range(len(indegrees)):
        if indegrees[idx] == 0:
            que.append(idx)

    # 2. 큐가 빌 때까지 반복
    while que:
        # 2-1. 큐에서 노드 꺼내기
        cur = que.popleft()
        result.append(cur)

        # 2-1. 해당 노드와 연결된 노드들의 진입차수에서 1 빼기
        for next in graph[cur]:
            indegrees[next] -= 1

            # 2-2. 새롭게 집입차수가 0이 된 노드를 큐에 삽입
            if indegrees[next] == 0:
                que.append(next)

    # 3. 모든 노드를 방문하기 전에 큐가 빈다면, 사이클이 존재하는 것이다.
    if any(indegrees):
        return False

    return True
```

<br>

## Reference

https://m.blog.naver.com/ndb796/221236874984
https://gmlwjd9405.github.io/2018/08/27/algorithm-topological-sort.html
https://freedeveloper.tistory.com/390
