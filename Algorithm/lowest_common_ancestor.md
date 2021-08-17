# 최소 공통 조상 (LCA, Lowest Common Ancestor)

## LCA 알고리즘이란

두 노드의 공통된 조상 중에서 가장 가까운 조상을 찾는다.

<br>

## LCA 알고리즘의 동작

1. 모든 노드의 깊이를 계산한다.
2. 최소 공통 조상을 찾을 두 노드의 깊이가 같도록 맞춘다.
3. 부모가 같아질 때까지 두 노드를 부모 방향으로 거슬러 올라간다.

<br>

## LCA 알고리즘의 시간 복잡도

부모를 거슬러 올라가야 하므로 최악의 경우 O(N)의 시간 복잡도가 요구된다.

<br>

## Python Code

```python
depth_list: list[int]  # depth_list[u]: 노드u의 깊이, 초기값 -1
parent_list: list[int]  # parent_list[u]: 노드u의 부모 노드
graph: list[list[int]]  # graph[u]: 노드u와 연결된 노드 리스트


# 각 노드의 깊이를 구한다.
def calculate_depth(node: int, depth: int) -> None:
    depth_list[node] = depth
    for child in graph[node]:
        if depth_list[child] != -1:
            continue
        parent_list[child] = node
        calculate_depth(child, depth + 1)


# u와 v의 최소 공통 조상을 찾는다.
def lca(u: int, v: int) -> int:
    # 두 노드의 깊이가 같도록 맞춘다.
    while depth_list[u] != depth_list[v]:
        if depth_list[u] > depth_list[v]:
            u = parent_list[u]
        else:
            v = parent_list[v]
    # 최소 공통 조상을 찾는다.
    while u != v:
        u = parent_list[u]
        v = parent_list[v]
    return u
```

<br>

## 개선된 LCA 알고리즘 (DP)

- 부모를 거슬러 올라갈 때 2의 제곱 형태로 거슬러 올라가면 O(logN)의 시간 복잡도를 보장할 수 있다.
- 이를 위해 각 노드에 대해 2^i번째 부모를 저장한다.

<br>

## Python Code

```python
LOG: int  # log(트리가 가질 수 있는 최대 깊이)

n: int  # 노드의 개수
depth_list: list[int]  # depth_list[u]: 노드u의 깊이, 초기값 -1
parent_list: list[list[int]]  # parent_list[u][i]: 노드u의 2^i번째 부모 노드
graph: list[list[int]]  # graph[u]: 노드u와 연결된 노드 리스트


# 각 노드의 깊이를 구한다.
def calculate_depth(node: int, depth: int) -> None:
    depth_list[node] = depth
    for child in graph[node]:
        if depth_list[child] != -1:
            continue
        parent_list[child][0] = node
        calculate_depth(child, depth + 1)


# 전체 부모 관계를 설정한다.
# 현재 노드의 2^i번째 부모 노드는 2^(i-1)번째 부모 노드의 2^(i-1)번째 부모 노드이다.
def set_parent() -> None:
    calculate_depth(1, 0)
    for i in range(1, LOG):
        for node in range(1, n + 1):
            parent_list[node][i] = parent_list[parent_list[node][i - 1]][i - 1]


# u와 v의 최소 공통 조상을 찾는다.
def lca(u: int, v: int) -> int:
    # v가 더 깊도록 설정한다.
    if depth_list[u] > depth_list[v]:
        u, v = v, u
    # 두 노드의 깊이가 같도록 맞춘다.
    for i in range(LOG - 1, -1, -1):
        if depth_list[v] - depth_list[u] >= (1 << i):
            v = parent_list[v][i]
    # 최소 공통 조상을 찾는다.
    if u == v:
        return u
    for i in range(LOG - 1, -1, -1):
        if parent_list[u][i] != parent_list[v][i]:
            u = parent_list[u][i]
            v = parent_list[v][i]
    return parent_list[u][0]
```

<br>

## References

https://www.youtube.com/watch?v=O895NbxirM8