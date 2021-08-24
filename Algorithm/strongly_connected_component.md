# 강한 결합 요소 (Strongly Connected Component)

## SCC란

- SCC란 그래프 안에서 '강하게 결합된 정점 집합'이라는 의미로, 같은 SCC에 속하는 두 정점은 서로 도달이 가능하다.
- 기본적으로 사이클이 발생하는 경우 무조건 SCC에 해당한다.

<br>

## SCC 알고리즘의 종류

- 코사라주 알고리즘 (Kosaraju's algorithm)
- 타잔 알고리즘 (Tarjan's algorithm)
    - O(v + E)의 시간 복잡도를 갖는다.

<br>

## 타잔 알고리즘이란

- 모든 정점에 대해 DFS를 수행하여 SCC를 찾는 알고리즘이다.

<br>

## Python Code

```python
id_: int
id_list: list[int]
stack: list[int]
finished: list[bool]
edge_list: list[list[int]]  # edge_list[node]: node에서 향할 수 있는 노드의 리스트
scc_list: list[list[int]]  # scc 리스트


def dfs(node: int) -> int:
    id_ += 1
    id_list[node] = id_
    stack.append(node)

    parent: int = id_list[node]
    for next_node in edge_list[node]:
        if id_list[next_node] == 0:
            parent = min(parent, dfs(next_node))
        elif not finished[next_node]:
            parent = min(parent, id_list[next_node])
    
    if parent == id_list[node]:
        scc: list[int] = []
        while True:
            prev = stack.pop()
            scc.append(prev)
            finished[prev] = True
            if prev == node:
                break
        scc_list.append(scc)
    
    return parent


def main() -> None:
    node_list: list[int]
    for node in node_list:
        if id_list[node] == 0:
            dfs(node)
```

<br>

## References

https://blog.naver.com/PostView.nhn?blogId=ndb796&logNo=221236952158