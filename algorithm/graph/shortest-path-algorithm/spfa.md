# SPFA(Shortest Path Faster Algorithm)

## SPFA란

SPFA는 벨만-포드 알고리즘을 개선한 알고리즘입니다. 벨만-포드 알고리즘은 모든 간선에 대해 갱신을 진행하지만, SPFA는 최소값이 갱신되는 정점과 연결된 간선에 대해서만 갱신을 진행합니다.

### 동작

1. 시작 정점을 Queue에 넣습니다.
2. Queue에서 맨 앞 정점을 꺼내서, 연결된 간선을 확인합니다. 만약, 꺼낸 정점과 연결된 정점이 최단 거리를 갱신할 수 있으면 갱신하고, Queue에 들어있지 않으면 Queue에 넣습니다.
3. Queue가 빌 때까지 1 ~ 2를 반복합니다.

#### 음의 사이클 확인

Queue에 정점을 넣을 때마다, 카운트를 1씩 더합니다. 한 정점에 대해 카운트가 V가 되면, 음의 사이클이 존재한다고 판단합니다.

### 시간복잡도

간선을 볼 때마다 갱신이 이루어지는 최악의 경우 O(VE)이지만, 일반적으로 O(V + E)의 성능을 낸다고 알려져 있습니다.

<br>

## Python Code

```python
from collections import deque


n: int  # 정점의 개수
cost_list: list[int]  # cost_list[node]: 시작 정점에서 node까지 최소 비용
edge_list: list[list[tuple[int, int]]]  # edge_list[node]: (node와 연결된 정점, 해당 간선의 비용)


# 음의 사이클이 존재하면 True, 존재하지 않으면 False를 리턴
def spfa(start: int) -> bool:
    count_list: list[int] = [0 for _ in range(n)]  # count_list[node]: node가 queue에 들어간 횟수. 음의 사이클 확인을 위해 사용
    in_que: list[bool] = [False for _ in range(n)]  # in_que[node]: node가 queue에 존재하면 True
    que: deque[int] = deque()

    cost_list[start] = 0
    in_que[start] = True
    que.append(start)

    while que:
        node = que.popleft()
        in_que[node] = False
        for adj_node, cost in edge_list[node]:
            # 갱신이 이루어지는 정점에 대해서만 이후 과정을 진행
            if cost_list[adj_node] > cost_list[node] + cost:
                cost_list[adj_node] = cost_list[node] + cost
                if not in_que[adj_node]:
                    count_list[adj_node] += 1
                    if count_list[adj_node] == n:
                        return True
                    in_que[adj_node] = True
                    que.append(adj_node)
    return False
```

<br>

## Reference

https://huisam.tistory.com/entry/Bellman-Ford-SPFA
