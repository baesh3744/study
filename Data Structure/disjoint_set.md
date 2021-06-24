# Disjoint-set (union-find)

## Disjoint-set이란?

---

-   서로 중복되지 않는 부분 집합들로 나눠진 원소들에 대한 정보를 저장하고 조작하는 자료구조

-   다수의 노드들 중에서 연결된 노드를 찾거나 노드들을 합칠 때 사용

<br>

<br>

## Disjoint-set 구현

---

-   초기화: N개의 원소가 각각의 집합에 포함되어 있도록 초기화

-   Union 연산: 두 원소 a, b가 주어질 때, 이들이 속한 두 집합을 하나로 합침

-   Find 연산: 어떤 원소 a가 주어질 떄, 이 원소가 속한 집합을 반환

<br>

### 배열을 사용한 방법

1. 원소의 크기만큼 배열을 초기화 (생성) 해준다.

2. 두 원소를 합치기 위해 배열의 모든 원소를 순회하면서 하나의 번호를 나머지 하나로 교체한다. (두 원소들을 합칠 때, 해당 원소의 연결정보를 찾는 연산은 한 번만에 알 수 있다.)

-   Uion 연산에서 배열의 모든 원소를 순회하므로 시간복잡도는 O(N) -> 트리 구조를 사용한 방법이 더 효율적이다.

```python
class DisjointSet:
    def __init__(self, n):
        self.data = list(range(n))
        self.size = n

    def find(self, index):
        return self.data[index]

    # 재귀함수를 이용한 방법도 존재하지만,
    # 재귀함수를 이용하면 연결 정보의 순서가 바뀌면 동작에 문제가 생긴다.
    # ex. union(1, 2)는 정상동작하지만, union(2, 1)은 에러가 발생
    def union(self, x, y):
        xset, yset = self.find(x), self.find(y)

        if xset == yset:
            return

        for idx in range(self.size):
            if self.find(idx) == y:
                self.data[idx] = x
```

<br>

### 트리 구조를 사용한 방법 - Union-by-size

-   원소가 적은 집합을 원소가 많은 집합의 하위 트리로 합치는 방법

-   트리의 경우, 높이는 logn이므로 시간복잡도는 O(logn)

```python
class DisjointSet:
    def __init__(self, n):
        # data[index]: index != 루트노드이면, 그룹 번호
        #              index == 루트노드이면, -1 * 그룹에 속한 원소의 개수
        self.data = [-1 for _ in range(n)]
        # 그룹의 개수
        self.size = n

    # index가 속한 그룹 번호를 리턴
    def find(self, index):
        value = self.data[index]
        if value < 0:
            return index
        return self.find(value)

    def union(self, x, y):
        x = self.find(x)
        y = self.find(y)

        if x == y:
            return

        if self.data[x] < self.data[y]:
            self.data[x] += self.data[y]
            self.data[y] = x
        else:
            self.data[y] += self.data[x]
            self.data[x] = y

        self.size -= 1
```

<br>

### 트리 구조를 사용하는 방법 - Union-by-height

-   트리의 높이가 낮은 집합을 높이가 높은 집합의 서브트리로 합치는 방법

```python
class DisjointSet:
    def __init__(self, n):
        # data[index]: index != 그룹의 시작이면, 그룹의 시작 번호
        #              index == 그룹의 시작이면, -1 * 2
        self.data = [-1 for _ in range(n)]
        self.size = n

    def find(self, index):
        value = self.data[index]
        if value < 0:
            return index
        return self.find(value)

    def union(self, x, y):
        x = self.find(x)
        y = self.find(y)

        if x == y:
            return

        if self.data[x] < self.data[y]:
            self.data[y] = x
        elif self.data[x] > self.data[y]:
            self.data[x] = y
        else:
            self.data[x] -= 1
            self.data[y] = x

        self.size -= 1
```

<br>

### 트리 구조를 사용하는 방법 - Path-comprehension

-   Union-by-size나 union-by-height는 find() 연산을 수행할 때 트리의 높이만큼 올라가 루트를 찾을 수 있는데, 이러한 비효율성을 해결하고자 나온 개념이다. Path comprehension을 수행하면 루트를 찾는 find() 연산 비용을 낮출 수 있다.

```python
class DisjointSet:
    def __init__(self, n):
        self.data = [-1 for _ in range(n)]
        self.size = n

    def upward(self, change_list, index):
        value = self.data[index]
        if value < 0:
            return index

        change_list.append(index)
        return self.upward(change_list, value)

    def find(self, index):
        change_list = []
        result = self.upward(change_list, index)

        for idx in change_list:
            self.data[idx] = result

        return result

    def union(self, x, y):
        x = self.find(x)
        y = self.find(y)

        if x == y:
            return

        if self.data[x] < self.data[y]:
            self.data[y] = x
        elif self.data[x] > self.data[y]:
            self.data[x] = y
        else:
            self.data[x] -= 1
            self.data[y] = x

        self.size -= 1
```

<br>

<br>

## References

---

https://brownbears.tistory.com/460
