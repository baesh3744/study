# 단절점 (Articulation Point) 과 단절선 (Articulation Bridge)

## 단절점이란

-   하나의 컴포넌트로 구성된 무방향 그래프에서 특정 정점을 제거하였을 때 두 개 이 상의 컴포넌트 (그래프) 로 나누어지면, 그 정점을 단절점이라고 한다.

<br>

## 단절점을 구하는 방법

-   모든 정점을 한 번씩 선택하여 제거한 후 (O(V)), 그래프가 나누어지는지 확인한다. (O(V+E))
    -   시간 복잡도 O(V\*(V+E))로 매우 비효율적인 방법이다.
-   단절점의 특성을 이용한다.
    -   단절점의 특성: 단절점에 인접해있는 정점들은 단절점이 없으면 우회로로 인해 연결되어있지 않아야 한다.
    -   DFS를 이용하여 정점의 방문 순서를 기록해둔다. 특정 정점 A과 연결된 정점들이 정점 A를 거치지 않고 정점 A보다 빠른 방문 번호를 가진 정점으로 갈 수 없다면 단절점이다.
    -   루트 노드의 경우에는 연결된 노드의 수가 2개 이상이면 단절점이다.
    -   한 번의 DFS로 단절점들을 구할 수 있으므로 O(V+E)의 시간 복잡도를 가진다.

<br>

## Python Code

```python
dsc_number: int  # 방문 순서
discovered: list[int]  # discovered[node]: node의 방문 순서
is_apoint: list[bool]  # is_apoint[node]: node가 단절점인가
edge_list: list[list[int]]  # edge_list[node]: node와 연결된 정점 리스트


# 연결된 정점 중 가장 빠른 방문 번호를 리턴한다.
def dfs(node: int, is_root: bool) -> int:
    # DFS 방문 순서 지정
    discovered[node] = dsc_number
    dsc_number += 1

    # ret: node와 연결된 정점 중 가장 빠른 방문 번호
    ret: int = discovered[node]
    # count: 자식 수
    count: int = 0

    for next_node in edge_list[node]:
        if discovered[next_node] != 0:
            ret = min(ret, discovered[next_node])
            continue

        child += 1
        prev: int = dfs(next_node, False)
        if not is_root and prev >= discovered[node]:
            is_apoint[node] = True
        ret = min(ret, prev)

    if is_root:
        is_apoint[node] = (child >= 2)

    return ret


def main() -> None:
    node_list: list[int]
    for node in node_list:
        if discovered[node] == 0:
            dfs(node, True)
```

<br>

## 단절선이란

-   단절점과 유사하지만 정점이 아닌 간선을 제거하였을 때, 그래프가 두 개 이상으로 나누어지는 간선을 말한다.

<br>

## 단절선을 구하는 방법

-   단절점을 구하는 방법과 유사하다. DFS를 이용하여 정점의 방문 순서를 기록해두고, 특정 정점 A과 연결된 정점 B가 정점 A를 거치지 않고 정점 A보다 빠른 방문 번호를 가진 정점으로 갈 수 없다면 정점 A와 정점 B를 잇는 간선이 단절선이다.

<br>

## Python Code

```python
dsc_number: int  # 방문 순서
discovered: list[int]  # discovered[node]: node의 방문 순서
abridge_list: list[tuple[int, int]]  # 단절선 리스트
edge_list: list[list[int]]  # edge_list[node]: node와 연결된 정점 리스트


def dfs(node: int, parent: int) -> int:
    # DFS 방문 순서 지정
    discovered[node] = dsc_number
    dsc_number += 1

    # ret: node와 연결된 정점 중 가장 빠른 방문 번호
    ret: int = discovered[node]

    for next_node in edge_list[node]:
        if next_node == parent:
            continue

        if discovered[next_node] != 0:
            ret = min(ret, discovered[next_node])
            continue

        prev: int = dfs(next_node, node)
        if prev > discovered[node]:
            abridge_list.append((node, next_node))
        ret = min(ret, prev)

    return ret


def main() -> None:
    node_list: list[int]
    for node in node_list:
        if discovered[node] == 0:
            dfs(node, 0)
```

<br>

## Reference

https://www.crocus.co.kr/1164
