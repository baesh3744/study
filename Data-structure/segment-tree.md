# 세그먼트 트리 (Segment Tree)

## 세그먼트 트리란?

트리의 일종으로, 주어진 쿼리에 대해 빠르게 응답하기 위해 만들어진 자료구조이다. 여기서 **주어진 쿼리**란 특정 구간의 합, 곱, 최솟값, 최댓값 등을 구하는 것을 말한다.

![세그먼트 트리](https://onlinejudgeimages.s3-ap-northeast-1.amazonaws.com/blog/seg1.png)
[이미지 출처] https://www.acmicpc.net/blog/view/9

-   리프 노드: 배열의 수 자체
-   다른 노드: 왼쪽 자식과 오른쪽 자식의 합 또는 어떠한 동작의 결과를 저장

<br>

## 세그먼트 트리의 시간복잡도

| 동작                  | 선형구조 | 세그먼트 트리 |
| --------------------- | -------- | ------------- |
| 초기화                | O(N)     | O(logN)       |
| 특정 구간에 대한 동작 | O(N))    | O(logN)       |
| 특정 인덱스의 값 변경 | O(1)     | O(logN)       |

<br>

## Python code

```python
data: list[int]  # 값이 저장된 배열, len = N
tree: list[int]  # 세그먼트 트리, len = 2^(math.ceil(log2(N))+1), 일반적으로 4*N으로 생성한다.

# 초기화
def init(node: int, start: int, end: int) -> int:
    if start == end:
        tree[node] = data[start]
        return tree[node]

    mid = (start + end) // 2
    tree[node] = init(2*node, start, mid) + init(2*node+1, mid+1, end)
    return tree[node]


# 구간합 구하기
# node가 담당하는 구간이 start ~ end
# 구해야하는 합의 범위는 left ~ right
def sum_(node: int, start: int, end: int, left: int, right: int) -> int:
    if right < start or end < left:
        return 0
    if left <= start and end <= right:
        return tree[node]

    mid = (start + end) // 2
    return sum_(2*node, start, mid, left, right) + sum_(2*node+1, mid+1, end, left, right)


# 값 변경하기
# index번째 수를 val로 변경할 때, diff = val - data[index]
def update(node: int, start: int, end: int, index: int, diff: int) -> None:
    if index < start or end < index:
        return

    tree[node] += diff
    if start != end:
        mid = (start + end) // 2
        update(2*node, start, mid, index, diff)
        update(2*node+1, mid+1, end, index, diff)
```

<br>

## Reference

https://www.acmicpc.net/blog/view/9
