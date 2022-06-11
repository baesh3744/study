# 호프크로프트-카프 알고리즘(Hopcroft-Karp Algorithm)

## 호프크로프트-카프 알고리즘이란

간선 용량이 1인 이분 그래프에서 최대 매칭을 찾는 알고리즘입니다. (이분 매칭보다 빠릅니다.)

디닉 알고리즘을 모든 간선의 용량이 1인 이분 그래프 환경으로 국한되게 만든 결과물입니다.

### 시간복잡도

시간복잡도는 O(E√V)입니다. 완전 이분 그래프에서 E = O(V^2)이므로 최악의 경우 O(V^2.5)가 됩니다.

### 새로운 용어

1. 교차 경로(alternating path)
    - 그래프 위의 경로인데, 어떤 매칭 M에 대해 매칭에 속한 간선 - 속하지 않는 간선 - 속한 간선 - 속하지 않는 간선 - ... 처럼 간선이 매칭에 속하는지에 대한 여부가 번갈아 반복되는 경로를 말합니다.
2. 증가 경로(augmenting path)
    - 양 끝점은 매칭에 속하지 않고, 나머지 경로가 교차 경로를 이루는 경로를 말합니다.

### 아이디어

증가 경로가 존재하면 반드시 매칭 크기를 1 늘릴 수 있습니다.

-   why? 어떤 증가 경로가 존재하면, 해당 경로는 간선이 매칭에 안 속함 - 속함 - 안 속함 - ... - 속함 - 안 속함 상태입니다. 이 경로에서 모든 간선의 속함 여부를 뒤집어버리면, 매칭에 속한 간선 수가 1 증가하게 됩니다.

<br>

## 호프크로프트-카프 알고리즘의 동작

### 과정

1. 매칭 M이 공집합인 상태로 시작합니다.
2. 정점, 간선이 겹치지 않는 증가 경로의 집합 P를 찾습니다.
3. M = M ⊕ P (⊕는 XOR연산)
4. P가 공집합이면 종료하고, 아니면 2로 돌아가 반복합니다.

### 예시

1. 파란 그룹을 A, 빨간 그룹을 B라 하고, 빨간선이 매칭이라고 가정해 봅시다.

<p align="center">
    <img src="https://user-images.githubusercontent.com/22341362/145768135-fa609a3b-a681-42b1-940b-17140e290e7e.png" width=200>
</p>

2. 아래와 같이 레벨을 매길 수 있습니다.
    - 레벨 0인 정점을 먼저 정하고 들어갑니다. A그룹에 속해 있고 (B 그룹이어도 상관없습니다) 매칭에 속하지 않은 정점들이 레벨 0입니다.
    - BFS를 사용해, 다른 정점들의 레벨을 매깁니다. 이때, 다른 정점들의 레벨은 모든 레벨 0인 정점과의 최단거리 중 최소값이 됩니다.

<p align="center">
    <img src="https://user-images.githubusercontent.com/22341362/145768325-0978d3b3-a6dd-4fb9-8d52-faeaa5036199.png" width=200>
</p>

3. 아래와 같이 증가 경로 (빨간선) 을 찾을 수 있습니다. 이때, 이분 매칭처럼 증가 경로끼리 경로를 교환할 수 있습니다. 원래 간선 A5 - B5가 이전에 찾은 증가 경로 중에 있었는데 (2번째 그림), 이를 A5 - B6으로 대체하면, 새로운 증가 경로 A6 - B4 - A3 - B5를 얻을 수 있습니다 (3번째 그림).

<p align="center">
    <img src="https://user-images.githubusercontent.com/22341362/145768328-245300f7-36c5-4c93-9e32-1cd0233525ff.png" width=200>
    <img src="https://user-images.githubusercontent.com/22341362/145768333-cf8f7f27-6c36-4e5a-9469-1403306af330.png" width=200>
    <img src="https://user-images.githubusercontent.com/22341362/145768342-e831f363-5225-4dfc-b2fa-51134fcf659e.png" width=200>
</p>

4. 결과적으로 증가 경로를 3개 찾았기 때문에, 매칭 크기는 3이 늘어납니다.

<p align="center">
    <img src="https://user-images.githubusercontent.com/22341362/145768346-01b02756-8779-4674-a145-ed85e1295bf7.png" width=200>
</p>

<br>

## Python Code

```python
from collections import deque


INF  # Infinity value
A: list[int]  # A[a] - A그룸의 정점 a와 연결된 B 그룹의 정점
B: list[int]  # B[b] - B그룹의 정점 b와 연결된 A 그룹의 정점
levels: list[int]  # levels[a] - A그룹의 정점 a의 레벨
matched: list[bool]  # matched[a] - A그룹의 정점 a가 매칭에 속해있는가
adj: list[list[int]]


# A그룹 정점에 레벨을 매깁니다.
def bfs() -> None:
    que: deque[int] = deque()

    # 매칭에 속하지 않는 A그룹의 정점만 레벨 0으로 시작합니다.
    for a, ismatched in enumerate(matched):
        if ismatched:
            levels[a] = INF
        else:
            levels[a] = 0
            que.append(a)

    # BFS를 통해 A그룹 정점에 레벨을 매깁니다.
    while que:
        a = que.popleft()
        for b in adj[a]:
            if B[b] != -1 and levels[B[b]] == INF:
                levels[B[b]] = levels[a] + 1
                que.append(B[b])


# 새 매칭을 찾으면 True
def dfs(a: int) -> bool:
    for b in adj[a]:
        if B[b] == -1 or (levels[B[b]] == levels[a] + 1 and dfs(B[b])):
            matched[a] = True
            A[a] = b
            B[b] = a
            return True
    return False


def hopcroft_karp() -> int:
    matching: int = 0

    while True:
        # 각 정점에 레벨을 매깁니다.
        bfs()

        # 이분 매칭과 비슷하게 A그룹 정점을 순회하며 매칭 증가량을 찾습니다.
        flow: int = 0
        for a, ismatched in enumerate(matched):
            if not ismatched and dfs(a):
                flow += 1

        # 더 이상 증가 경로를 못 찾으면 알고리즘을 종료합니다.
        if flow == 0:
            break

        # 찾았을 경우, 위 과정을 반복합니다.
        matching += flow

    return matching
```

<br>

## Reference

-   [라이(kks227) - "호프크로프트 카프 알고리즘 (Hopcroft-Karp Algorithm) (수정: 2020-09-30)"](https://blog.naver.com/PostView.nhn?isHttpsRedirect=true&blogId=kks227&logNo=220816033373)
