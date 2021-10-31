# 벨만-포드 알고리즘(Bellman-Ford Algorithm)

## 벨만-포드 알고리즘이란

벨만-포드 알고리즘이란 한 정점에서 다른 정점까지의 최단 거리를 구하는 알고리즘입니다. 벨만-포드 알고리즘은 다익스트라 알고리즘과 다르게 가중치가 음수일 때도 사용이 가능합니다.

### 음수 가중치의 문제

음의 가중치 자체가 문제가 되는 것은 아니지만, 음의 값이 누적되는 사이클이 존재하면 사이클을 돌 때마다 가중치가 줄어드므로 문제가 발생합니다.

정점의 개수가 N개인 경우, 최단 경로의 길이는 최대 N - 1이 됩니다. 따라서, 거리의 갱신을 (정점의 개수 - 1)번만 반복하면, 모든 정점까지의 최단 거리를 구할 수 있습니다. 이 후, 한 번 더 거리의 갱신을 시도하였을 때, 갱신이 이루어진다면 음의 사이클이 존재한다고 판단할 수 있습니다.

### 시간복잡도

시간복잡도는 O(VE)입니다. (V: 정점 개수, E: 간선 개수)

<br>

## Python Code

```python
n: int  # 정점의 개수
cost_list: list[int]  # cost_list[node]: 시작 정점로부터 node까지의 거리, 초기값 INF
graph: list[tuple[int, int, int]]  # 간선 정보를 저장. (start, end, cost) ---> start에서 end까지 가는데 cost의 비용이 발생합니다.


# 음의 사이클이 존재하면 True, 존재하지 않으면 False를 리턴
def bellman_ford(start: int) -> bool:
    # 시작 정점에 대한 비용 초기화
    cost_list[start] = 0

    # 최단 거리 구하기
    for _ in range(n - 1):
        for _start, _end, _cost in graph:
            if cost_list[_start] != INF and cost_list[_start] + cost < cost_list[_end]:
                cost_list[_end] = cost_list[_start] + cost

    # 음의 사이클 확인
    for _start, _end, _cost in graph:
        if cost_list[_start] != INF and cost_list[_start] + cost < cost_list[_end]:
            return True
    return False
```

아래와 같이 구현할 수도 있습니다.

```python
def bellman_ford(start: int) -> bool:
    cost_list[start] = 0

    # 최단 거리 갱신을 음의 사이클 확인을 위해 n번 진행
    for iter_ in range(n):
        for _start, _end, _cost in graph:
            if cost_list[_start] != INF and cost_list[_start] + cost < cost_list[_end]:
                cost_list[_end] = cost_list[_start] + cost
                # 마지막 단계에서 갱신이 이루어진다면, 음의 사이클이 존재한다고 판단
                if iter_ == n - 1:
                    return True
    return False
```

<br>

## Reference

https://developer-davii.tistory.com/89

https://codingexplore.tistory.com/58
