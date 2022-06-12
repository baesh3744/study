# 최소 스패닝 트리 (MST, Minimum Spanning Tree)

## Spanning Tree

### Spanning Tree란

-   Spanning Tree란 그래프의 최소 연결 부분 그래프이다.
    -   최소 연결 = 간선의 수가 가장 적다.
    -   n개의 정점을 가지는 그래프의 최소 간선의 수는 n-1개이고, n-1개의 간선으로 연결되어 있으면 필연적으로 트리 형태가 되고 이것이 Spanning Tree이다.
-   즉, 그래프에서 일부 간선을 선택해서 만든 트리이다.

### Spanning Tree의 특징

-   BFS, DFS를 이용하여 그래프에서 Spanning Tree를 찾을 수 있다.
    -   탐색 도중에 사용된 간선을 모으면 만들 수 있다.
-   하나의 그래프에는 여러 개의 Spanning Tree가 존재할 수 있다.
-   Spanning Tree는 트리의 특수한 형태이므로 모든 정점들이 연결되어 있어야 하고 사이클을 포함해서는 안 된다.
-   따라서 Spanning Tree는 그래프에 있는 n개의 정점을 정확히 n-1개의 간선으로 연결한다.

<br>

## MST

### MST란

-   각 간선의 가중치가 동일하지 않을 때 단순히 가장 적은 간선을 사용한다고 해서 최소 비용이 얻어지는 것은 아니다.
-   MST는 Spanning Tree 중에서 간선들의 가중치의 합이 최소인 트리이다.

### MST의 특징

-   간선의 가중치의 합이 최소여야 한다.
-   n개의 정점을 가지는 그래프에 대해 반드시 n-1개의 간선만을 사용해야 한다.
-   사이클이 포함되어서는 안된다.

<br>

## MST 구현

### Kruskal MST vs. Prim MST

-   그래프 내에 적은 간선을 가지는 회소 그래프 (Sparse Graph) 의 경우, Kruskal 알고리즘이 적합하다.
-   그래프에 간선이 많이 존재하는 밀집 그래프 (Dense Graph) 의 경우, Prim 알고리즘이 적합하다.

<br>

### Kruskal MST

#### Kruskal MST란

-   Greedy를 이용하여 각 단계에서 사이클을 이루지 않는 최소 비용 간선을 선택한다.
    -   간선 선택을 기반으로 하는 알고리즘이다.
    -   이전 단계에서 만들어진 Spanning Tree와는 상관없이 무조건 최소 비용 간선만을 선택한다.

#### 동작

1. 그래프의 간선들을 가중치의 오름차순으로 정렬한다.
2. 정렬된 간선 리스트에서 순서대로 사이클을 형성하지 않는 간선을 선택한다.
    - 남아있는 간선 중에서 가중치가 가장 낮은 간선을 먼저 선택한다.
    - 사이클을 형성하는 간선은 제외한다.
3. 해당 간선을 현재 MST의 집합에 추가한다.

#### 주의할 점

-   새로운 간선을 추가할 때 사이클을 생성하는지 체크해야 한다.
-   사이클 생성 여부를 확인하는 방법
    -   추가하고자 하는 간선의 양 끝 정점이 같은 집합에 속해있는지를 먼저 검사해야 한다.
    -   'union-find 알고리즘' 이용

#### 시간 복잡도

-   union-find 알고리즘을 이용하면 Kruskal 알고리즘의 시간 복잡도는 간선들을 정렬하는 시간에 의해 결정된다.
-   간선이 E개 존재한다면, 시간 복잡도는 O(ElogE)가 된다.

#### Python Code

```python
verticles: list[str]  # 정점 리스트
edges: list[tuple[str, str, int]]  # 간선 리스트, (정점A, 정점B, 가중치)
parent: dict[str, str]  # 각 정점의 부모 정점
rank: dict[str, int]  # 각 정점의 rank


# path compression 기법
def find(node: str) -> str:
    if parent[node] != node:
        parent[node] = find(parent[node])
    return parent[node]


# union-by-rank 기법
def union(node_u: str, node_v: str) -> None:
    root_u, root_v = find(node_u), find(node_v)
    if rank[root_u] > rank[root_v]:
        parent[root_v] = root_u
    else:
        parent[root_u] = root_v
        if rank[root_u] == rank[root_v]:
            rank[root_v] += 1


def make_set(node: str) -> None:
    parent[node] = node
    rank[node] = 0


def kruskal() -> list[tuple[str, str, int]]:
    mst: list[tuple[str, str, int]] = list()

    # 1. 초기화
    for node in verticles:
        make_set(node)

    # 2. 간선 sorting
    sorted_edges = sorted(edges, key=lambda x: x[2])

    # 3. 간선 연결
    for edge in sorted_edges:
        node_u, node_v, weight = edge
        if find(node_u) != find(node_v):
            union(node_u, node_v)
            mst.append(edge)

    return mst
```

<br>

### Prim MST

#### Prim MST란

-   시작 정점에서부터 출발하여 Spanning Tree 집합을 단계적으로 확장해나가는 방법
    -   정점 선택을 기반으로 하는 알고리즘이다.
    -   이전 단계에서 만들어진 Spanning Tree를 확장하는 방법이다.

#### 동작

1. 시작 정점을 MST 집합에 추가한다.
2. 이전 단계에서 만들어진 MST 집합에 인접한 정점들 중에서 최소 비용 간선으로 연결된 정점을 선택하여 트리를 확장한다.
3. 위 과정을 트리가 n-1개의 간선을 가질 때까지 반복한다.

#### 시간 복잡도

-   모든 간선에 대해 반복하고, 최소 힙 구조를 사용하므로 시간 복잡도는 O(ElogE)가 된다.

#### Python Code

```python
from collections import defaultdict
from heapq import *


edges: list[tuple[str, str, int]]  # 간선 리스트, (가중치, 정점U, 정점V)


def prim(start_node: str) -> list[tuple[str, str, int]]:
    mst: list[tuple[str, str, int]] = list()

    adjacent_edges = defaultdict(list)
    for weight, node_u, node_v in adjacent_edges:
        adjacent_edges[node_u].append((weight, node_u, node_v))
        adjacent_edges[node_v].append((weight, node_v, node_u))

    connected_nodes = set(start_node)
    candidate_edge_list = adjacent_edges[start_node]
    heapify(candidate_edge_list)

    while candidate_edge_list:
    weight, node_u, node_v = heappop(candidate_edge_list)
        if node_v not in connected_nodes:
            connected_nodes.add(node_v)
            mst.append((weight, node_u, node_v))
            for edge in adjacent_edges[node_v]:
                if edge[1] not in connected_nodes:
                    heappush(candidate_edge_list, edge)

    return mst
```

<br>

## Reference

https://gmlwjd9405.github.io/2018/08/28/algorithm-mst.html

https://gmlwjd9405.github.io/2018/08/29/algorithm-kruskal-mst.html

https://gmlwjd9405.github.io/2018/08/30/algorithm-prim-mst.html

https://www.fun-coding.org/Chapter20-kruskal-live.html

https://www.fun-coding.org/Chapter20-prim-live.html
