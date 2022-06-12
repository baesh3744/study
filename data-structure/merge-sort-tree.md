# 머지 소트 트리(Merge Sort Tree)

머지 소트 트리는 세그먼트 트리의 변형으로, 각 노드에는 구간에 해당하는 배열이 정렬된 상태로 저장되어 있습니다.

### 시간복잡도

-   초기화: O(NlogN)
    -   Merge sort와 동일한 과정을 거치기 때문에 O(NlogN)을 가집니다.
-   쿼리 동작: O((logN)^2)
    -   구간은 최대 O(logN)개의 노드로 쪼갤 수 있고, 각 노드의 배열에 이분 탐색을 하면 O((logN)^2)에 쿼리를 처리할 수 있습니다.

<br>

## Python Code

```python
data: list[int]
tree: list[int]


# 초기화
def init(node: int, start: int, end: int) -> int:
    if start == end:
        tree[node] = [data[start]]
        return tree[node]

    mid: int = (start + end) // 2
    left_arr = init(2 * node, start, mid)
    right_arr = init(2 * node + 1, mid + 1, end)
    tree[node] = merge(left_arr, right_arr)
    return tree[node]


# Merge sort의 merge 함수와 동일하게 구현
def merge(left_arr: list[int], right_arr: list[int]) -> list[int]:
    merged: list[int] = []
    lidx, ridx, llength, rlength = 0, 0, len(left_arr), len(right_arr)
    while lidx < llength and ridx < rlength:
        if left_arr[lidx] <= right_arr[ridx]:
            merged.append(left_arr[lidx])
            lidx += 1
        else:
            merged.append(right_arr[ridx])
            ridx += 1
    while lidx < llength:
        merged.append(left_arr[lidx])
        lidx += 1
    while ridx < rlength:
        merged.append(right_arr[ridx])
        ridx += 1
    return merged
```

<br>

## Reference

https://yeoulcoding.tistory.com/130
