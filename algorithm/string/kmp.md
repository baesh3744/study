# KMP (Knuth, Morris, Pratt)

## KMP 알고리즘이란

텍스트의 길이가 N, 패턴의 길이가 M일 떄, 텍스트에서 일치하는 패턴의 개수를 O(N+M)에 찾아주는 알고리즘이다. (문자를 일일이 비교하는 방법으로는 O(NM)이 걸린다.)

<br>

## pi배열

-   pi[i]는 주어진 문자열의 0 ~ i까지의 부분 문자열 중에서 prefix(접두사) == suffix(접미사)가 될 수 있는 부분 문자열 중에서 가장 긴 것의 길이이다.
-   예. 문자열 "ABAABAB"의 pi배열
    | i | 부분 문자열 | pi[i] |
    |---|------------|-------|
    | 0 | A | 0 |
    | 1 | AB | 0 |
    | 2 | ABA | 1 |
    | 3 | ABAA | 1 |
    | 4 | ABAAB | 2 |
    | 5 | ABAABA | 3 |
    | 6 | ABAABAB | 2 |

<br>

## KMP 알고리즘의 동작

-   텍스트 "ABCDABCDABEE"에서 패턴 "ABCDABE"

| 인덱스 | 0   | 1   | 2   | 3   | 4   | 5   | 6   | 7   | 8   | 9   | 10  | 11  |
| ------ | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 텍스트 | A   | B   | C   | D   | A   | B   | C   | D   | A   | B   | E   | E   |
| 패턴   | A   | B   | C   | D   | A   | B   | E   |     |     |     |     |     |

-   첫 번째 시도에서 패턴의 0 ~ 5의 부분 문자열 ("ABCDAB") 는 텍스트와 일치했지만 6번째 인덱스의 E가 텍스트와 일치하지 않았다.
-   위 상황에서 "ABCDAB"에서 접두사 AB와 접미사 AB가 일치하기 때문에 아래 단계로 넘어갈 수 있다. 또한 pi[5] = 2를 통해 접두사와 접미사가 일치하는 최대 길이를 한 번에 구할 수 있다.

| 인덱스 | 0   | 1   | 2   | 3   | 4   | 5   | 6   | 7   | 8   | 9   | 10  | 11  |
| ------ | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 텍스트 | A   | B   | C   | D   | A   | B   | C   | D   | A   | B   | E   | E   |
| 패턴   |     |     |     |     | A   | B   | C   | D   | A   | B   | E   |     |

<br>

## Python Code

```python
def get_pi(pattern: str) -> list[int]:
    pi = [0 for _ in range(len(pattern))]
    prefix_index = 0
    for index, char in enumerate(pattern[1:], 1):
        while prefix_index > 0 and char != pattern[prefix_index]:
            prefix_index = pi[prefix_index - 1]
        if char == pattern[prefix_index]:
            prefix_index += 1
            pi[index] = prefix_index
    return pi


def kmp(text: str, pattern: str) -> list[int]:
    pi = get_pi(pattern)
    pattern_index, pattern_length = 0, len(pattern)
    start_list = []
    for index, char in enumerate(text):
        while pattern_index > 0 and char != pattern[pattern_index]:
            pattern_index = pi[pattern_index - 1]
        if char == pattern[pattern_index]:
            if pattern_index == patter_length - 1:
                start_list.append(index - pattern_index + 1)  # 시작 위치를 1로 했을 때
                pattern_index = pi[pattern_index]
            else:
                pattern_index += 1
    return start_list
```

<br>

## Reference

https://bowbowbow.tistory.com/6

https://girawhale.tistory.com/44
