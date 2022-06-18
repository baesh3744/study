# 최장 공통 부분수열 (LCS. Longest Common Subsequence)

> 2022.06.18 초안 작성

주어진 여러 수열에서 공통된 부분수열 중 길이가 가장 긴 것을 찾는 알고리즘입니다. (수열의 개수는 2개가 가장 일반적입니다.)

- e.g. ABAD와 ABCD의 최장 공통 부분수열은 ABD입니다.

부분수열이란 주어진 수열에서 요소의 순서를 변경하지 않고 일부 요소를 삭제해서 파생될 수 있는 수열을 말합니다. 예를 들어, pyt, pto, tn는 python의 부분수열입니다.

이러한 점에서 최장 공통 부분수열 알고리즘은 최장 공통 부분문자열 알고리즘 (LCS. Longest Common Substring) 과는 차이가 있습니다.

```python
def longest_common_subsequence(x: str, y: str) -> tuple[int, str]:
    """주어진 두 문자열의 최장 공통 부분수열을 구합니다.

    Args:
        x (str): 주어진 문자열 중 하나
        y (str): 나머지 문자열

    Returns:
        cache[x_length][y_length] (int): 최장 공통 부분수열의 길이. `len(subsequence)`와 같음
        subsequence (str): 최장 공통 부분수열
    """

    x_length, y_length = len(x), len(y)

    # cache[i][j]: x[:i + 1]과 y[:j + 1]의 최장 공통 부분수열의 길이
    cache = [[0 for _ in range(y_length + 1)] for _ in range(x_length + 1)]

    for x_index in range(1, x_length + 1):
        for y_index in range(1, y_length + 1):
            match = int(x[x_index - 1] == y[y_index - 1])

            cache[x_index][y_index] = max(
                cache[x_index - 1][y_index],
                cache[x_index][y_index - 1],
                cache[x_index - 1][y_index - 1] + match,
            )

    # 부분수열을 구합니다. 길이만 필요한 경우에는 생략할 수 있습니다.
    subsequence = ""
    x_index, y_index = x_length, y_length

    while x_index > 0 and y_index > 0:
        match = int(x[x_index - 1] == y[y_index - 1])

        if cache[x_index][y_index] == cache[x_index - 1][y_index - 1] + match:
            if match == 1:
                subsequence = x[x_index - 1] + subsequence
            x_index -= 1
            y_index -= 1
        elif cache[x_index][y_index] == cache[x_index - 1][y_index]:
            x_index -= 1
        else:
            y_index -= 1

    return cache[x_length][y_length], subsequence
```

## Reference

- https://github.com/TheAlgorithms/Python/blob/master/dynamic_programming/longest_common_subsequence.py
- https://en.wikipedia.org/wiki/Longest_common_subsequence_problem
- https://en.wikipedia.org/wiki/Subsequence
