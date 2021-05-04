# 배낭 문제(Knapsack Problem)

## 배낭 문제란?

---

-   담을 수 있는 최대 무게가 정해진 배낭과 함께 각각의 무게와 가치가 주어진 아이템의 집합이 주어졌을 때, 배낭에 담은 아이템들의 가치의 합이 최대가 되도록 하는 아이템들의 부분집합을 찾는 문제이다.

<br>

<br>

## 분할 가능한 배낭 문제(Fractional Knapsack Problem)

---

-   아이템을 쪼갤 수 있다고 가정한다. 무게당 가치가 높은 물건부터 담으면 된다.
-   Greedy algorithm으로 해결할 수 있다.

```python
def knapsack(capacity: int, weights: list[int], values: list[int]) -> int:
    ratios: list[tuple[int, int]] = []

    for idx in range(len(weights)):
        ratios.append((idx, values[idx] / weights[idx]))

    ratios.sort(key=lambda x: x[1], reverse=True)

    max_value: int = 0

    for idx, ratio in ratios:
        if weights[idx] <= capacity:
            capacity -= weights[idx]
            max_value += values[idx]
        else:
            max_value += (capacity * ratio)
            break

    return max_value
```

<br>

<br>

## 0-1 배낭 문제(0-1 Knapsack Problem)

---

-   아이템을 쪼갤 수 없다고 가정한다.
-   Dynamic Programming으로 해결할 수 있다.

<br>

### 재귀를 이용한 방법

```python
weights: list[int] = [ ... ]
values: list[int] = [ ... ]

# capacity: 남아있는 용량
# n: 아이템의 인덱스 번호
def knapsack(capacity: int, n: int) -> int:
    if capacity == 0 or n < 0:
        return 0
    if weights[n] > capacity:
        return knapsack(capacity, n - 1)
    return max(knapsack(capacity, n - 1),
               values[n] + knapsaxk(capacity - weights[n], n - 1))
```

<br>

### 2-D Array를 이용한 방법

```python
def knapsack(capacity: int, n: int, weights: list[int], values: list[int]) -> int:
    # cache[i][w] = 배낭 크기가 w일 때, index i 아이템까지 고려했을 때, 가능한 최대 가치
    cache: list[list[int]] = [[0 for _ in range(capacity + 1)] for _ in range(n + 1)]

    for idx in range(n):
        for weight in range(1, capacity + 1):
            if weights[idx] <= weight:
                cache[idx + 1][weight] = max(cache[idx][weight],
                                             cache[idx][weight - weights[idx]] + values[idx])
            else:
                cache[idx + 1][weight] = cache[idx][weight]

    return cache[n][capacity]
```

<br>

### 1-D Array를 이용한 방법

```python
def knapsack(capacity: int, n: int, weights: list[int], values: list[int]) -> int:
    # cache[w] = 배낭 크기가 w일 때, 가능한 최대 가치
    cache: list[int] = [0 for _ in range(capacity + 1)]

    for idx in range(n):
        for weight in range(capacity, weights[idx] - 1, -1):
            cache[weight] = max(cache[weight],
                                cache[weight - weights[idx]] + cache[idx])

    return cache[capacity]
```

<br>

<br>

## 출처

---

https://comdoc.tistory.com/entry/35-%EB%B0%B0%EB%82%AD%EB%AC%B8%EC%A0%9CKnapsack-problem-%ED%8C%8C%EC%9D%B4%EC%8D%AC

https://dojinkimm.github.io/algorithm/2019/10/19/dp-2.html
