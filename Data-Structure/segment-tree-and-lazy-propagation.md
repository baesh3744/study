# 느리게 갱신되는 세그먼트 트리

[일반적인 세그먼트 트리](segment-tree.md)에서 구간을 한 번에 갱신할 때 시간 복잡도는 O(NlogN)이 됩니다. 이러한 상황에서는 느리게 갱신되는 세그먼트 트리가 효율적입니다.

느리게 갱신되는 세그먼트 트리는 모든 값을 그때그때 갱신하지 않습니다. 각 노드마다 lazy 값을 두고, 굳이 갱신할 필요가 없는 노드에 대해서는 lazy 값을 조정해 나중에 해당 노드를 방문할 때까지 갱신을 미루어 시간적인 효율을 늘립니다.

<br>

## 느리게 갱신되는 세그먼트 트리의 동작

1. 각 노드를 방문하면, lazy 값을 살펴 미루어진 갱신을 진행합니다.
2. 현재 노드의 범위가 갱신하고자 하는 범위에 걸쳐진다면, 자식 노드로 재귀합니다.
3. 현재 노드의 범위가 갱신하고자 하는 범위에 포함된다면, 구간의 크기만큼 현재 노드의 값을 갱신하고 자식 노드에 lazy 값을 물려줍니다.

<br>

## Python Code

```python
data: list[int]
tree: list[int]
lazy: list[int]


# 초기화
def init(node: int, start: int, end: int) -> int:
    # 일반적인 세그먼트 트리와 동일


# 미루어진 갱신 진행
def update_lazy(node: int, start: int, end: int) -> None:
    if lazy[node] != 0:
        tree[node] += (end - start + 1) * lazy[node]
        if start != end:  # 구간 노드라면, 자식 노드에 lazy 값을 물려줍니다.
            lazy[2 * node] += lazy[node]
            lazy[2 * node + 1] += lazy[node]
        lazy[node] = 0


# 구간합 구하기
# node가 담당하는 구간이 start ~ end
# 구하고자 하는 구간의 범위는 left ~ right
def sum_(node: int, start: int, end: int, left: int, right: int) -> int:
    update_lazy(node, start, end)
    # 이후 과정은 일반적인 세그먼트 트리와 동일


# 구간값 갱신
def update(node: int, start: int, end: int, left: int, right: int, diff: int) -> None:
    update_lazy(node, start, end)

    if index < start or end < index:
        return

    # 완전히 포함되는 범위
    if left <= start and end <= right:
        tree[node] += (end - start + 1) * diff
        if start != end:
            lazy[2 * node] += diff
            lazy[2 * node + 1] += diff
        return

    # 걸치는 범위
    mid = (start + end) // 2
    update(2*node, start, mid, left, right, diff)
    update(2*node+1, mid+1, end, left, right, diff)
    tree[node] = tree[2 * node] + tree[2 * node + 1]
```

<br>

## Reference

https://4legs-study.tistory.com/128
