# Trie (트라이)

## 트라이란?

---

트라이란 문자열을 저장하고 효율적으로 탐색하기 위한 트리 형태의 자료구조이다. 래딕스 트리 (radix tree) 또는 접두사 트리 (prefix tree) 라고도 한다.

<br>

<br>

## 트라이의 사용 목적

---

문자열을 탐색할 때, 단순하게 하나씩 비교하면서 탐색하는 것보다 시간복잡도 측면에서 훨씬 효율적이다. 그러나 각 노드에서 자식들에 대한 포인터들을 배열로 모두 저장하고 있다는 점에서 저장 공간의 크기가 크다는 단점이 있다.

<br>

<br>

## 시간복잡도

---

-   제일 긴 문자열의 길이를 L, 문자열의 수를 M이라 한다.

-   생성 시간복잡도: O(M \* L)
    -   트라이 자료구조에 하나의 문자열을 삽입하는 것은 가장 긴 문자열의 길이만큼 걸리므로 O(L)만큼 걸린다. 모든 문자열을 넣어야 하므로 O(M \* L)만큼 걸린다.
-   탐색 시간복잡도: O(L)
    -   트리를 타고 들어가봤자 가장 긴 문자열의 길이만큼만 탐색하기 때문에 O(L)만큼 걸린다.

<br>

<br>

## 트라이 구조 예시

---

![https://media.vlpt.us/images/gojaegaebal/post/749ec3ff-e678-48e6-b20e-3f73ae288fed/image.png](https://media.vlpt.us/images/gojaegaebal/post/749ec3ff-e678-48e6-b20e-3f73ae288fed/image.png)
[이미지 출처]https://youseop.github.io/2020-11-09-BAEKJOON-14425_%EB%AC%B8%EC%9E%90%EC%97%B4%EC%A7%91%ED%95%A9

"abc" -> "ab" -> "car" 순으로 저장한다고 가정한다.

1. 먼저, "abc"를 추가한다.

    1-1. 첫 번째 문자는 "a"이다. 초기에 트라이 자료구조 내에는 아무것도 추가되어있지 않으므로 Head의 자식 노드에 "a"를 추가해준다.

    1-2. "a" 노드에도 현재 자식이 하나도 없으므로, "b"를 "a"의 자식 노드로 추가해준다.

    1-3. "c"도 마찬가지로 "b"의 자식 노드로 추가해준다.

    1-4. "abc"라는 단어가 여기서 끝남을 알려주기 위해서 현재 노드의 data에 "abc"를 저장한다.

2. 다음으로 "ab"를 추가한다.

    2-1. 현재 Head의 자식 노드로 "a"가 이미 존재한다. 따라서, "a" 노드를 추가하지 않고 기존에 있던 "a" 노드로 이동한다.

    2-2. "b"도 "a"의 자식 노드로 이미 존재하므로 "b" 노드로 이동한다.

    2-3. 여기서 "ab"라는 단어가 끝나므로 현재 노드의 data에 "ab"를 저장한다.

3. 마지막으로 "car"를 추가한다.

    3-1. Head의 자식 노드로 "a"만 존재하고 "c"는 존재하지 않는다. 따라서 "c"를 자식 노드로 추가한다.

    3-2. "a"와 "r"의 경우에도 위와 같은 방식으로 추가해주고, "r" 노드에서 단어가 끝이 나므로 "r" 노드의 data에 "car"를 저장한다.

<br>

<br>

## 구현

---

```python
# key: 해당 노드의 문자
# data: 해당 노드에서 끝나는 문자열
# children: 자식 노드
class Node:
    def __init__(self, key, data=None):
        self.key = key
        self.data = data
        self.children = {}

class Trie:
    def __init__(self):
        self.head = Node(None)

    def insert(self, string):
        cur_node = self.head
        for char in string:
            # 자식 노드들 중에서 같은 문자가 없으면 노드를 새로 생성
            if char not in cur_node.children:
                cur_node.children[char] = Node(char)
            # 문자에 해당하는 자식 노드로 이동
            cur_node = cur_node.children[char]
        # 문자열이 끝나는 노드의 data 값에 문자열 저장
        cur_node.data = string

    def search(self, string):
        cur_node = self.head
        for char in string:
            if char not in cur_node.children:
                return False
            cur_node = cur_node.children[char]
        if cur_node.data is None:
            return False
        return True
```

<br>

<br>

## References

---

https://velog.io/@gojaegaebal/210126-%EA%B0%9C%EB%B0%9C%EC%9D%BC%EC%A7%8050%EC%9D%BC%EC%B0%A8-%ED%8A%B8%EB%9D%BC%EC%9D%B4Trie-%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%9C%EB%85%90-%EB%B0%8F-%ED%8C%8C%EC%9D%B4%EC%8D%AC%EC%97%90%EC%84%9C-%EA%B5%AC%ED%98%84%ED%95%98%EA%B8%B0feat.-Class
