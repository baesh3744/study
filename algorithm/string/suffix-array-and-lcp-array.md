# 접미사 배열(Suffix Array)과 LCP 배열

## 접미사 배열(Suffix Array)

접미사 배열은 어떤 문자열의 접미사들을 사전순으로 나열하였을 때, 접미사를 나타내는 숫자의 배열을 의미합니다.

예를 들어, 문자열 S = banana 가 있을 때, 문자열 S의 접미사들은 아래와 같습니다.

| 접미사 | i   |
| -----: | --- |
| banana | 1   |
|  anana | 2   |
|   nana | 3   |
|    ana | 4   |
|     na | 5   |
|      a | 6   |

문자열 S의 접미사 배열은 아래와 같습니다.

| 접미사 | i   |
| ------ | --- |
| a      | 6   |
| ana    | 4   |
| anana  | 2   |
| banana | 1   |
| na     | 5   |
| nana   | 3   |

접미사 배열에서 문자열을 배열로 가지고 있으면 공간복잡도가 O(N<sup>2</sup>)이기 때문에 접미사를 나타내는 정수 i를 가지고 있습니다. 즉, 문자열 S의 접미사 배열은 [6, 4, 2, 1, 5, 3]입니다.

접미사 배열을 구하는 가장 쉬운 방법은 단순히 정렬하는 방법입니다. 문자열의 비교에 드는 시간은 O(N)이고 문자열이 N개 있으므로, 정렬하는 시간복잡도는 O(N<sup>2</sup>logN)입니다.

<br>

### 멘버-마이어스 알고리즘(Manber-Myers Algorithm)

접미사 배열을 구하는 알고리즘 중 가장 알려진 알고리즘으로 시간복잡도는 O(Nlog<sup>2</sup>N)입니다. 이 알고리즘은 첫 1, 2, 4, ..., 2<sup>n</sup> 글자를 기준으로 정렬을 반복합니다.

### 멘버-마이어스 알고리즘의 동작

<p>
    <img src='https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=http%3A%2F%2Fcfile22.uf.tistory.com%2Fimage%2F245A153C58B1315A1F8672' width='300px' />
</p>

정렬되지 않은 접미사들이 있습니다.

우선 첫 한 글자를 기준으로 정렬합니다.

<p>
    <img src='https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=http%3A%2F%2Fcfile26.uf.tistory.com%2Fimage%2F232F244358B133442A2FF9' width='300px' />
</p>

이후 첫 두 글자 기준으로 정렬합니다.

<p>
    <img src='https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=http%3A%2F%2Fcfile21.uf.tistory.com%2Fimage%2F2304CD4E58B134CB2C664C' width='300px' />
</p>

이후 첫 네 글자를 기준으로 정렬합니다.

<p>
    <img src='https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=http%3A%2F%2Fcfile21.uf.tistory.com%2Fimage%2F2143164C58B135FF0EBB84' width='300px' />
</p>

문자열 S의 길이가 6이므로 이 후로는 정렬할 필요가 없습니다.

### Python Code

```python
def get_suffix_array(string: str) -> list[int]:
    t: int = 1
    length: int = len(string)
    suffix_array: list[int] = [idx for idx in range(length)]
    group_list: list[int] = [0 for _ in range(length + 1)]

    for idx, char in enumerate(string):
        group_list[idx] = ord(char)

    while t < length:
        suffix_array.sort(key=lambda x: (group_list[x], group_list[min(x + t, length)]))

        new_group_list: list[int] = [0 for _ in range(length + 1)]
        new_group_list[suffix_array[0]] = 0
        new_group_list[-1] = -1

        for idx in range(1, length):
            left, right = suffix_array[idx - 1], suffix_array[idx]
            new_group_list[right] = new_group_list[left]
            if (
                group_list[left] != group_list[right]
                or group_list[min(left + t, length)] != group_list[min(right + t, length)]
            ):
                new_group_list[right] += 1

        group_list = new_group_list[:]
        t <<= 1

    return suffix_array
```

<br>

## LCP(Longest Common Prefix) 배열

LCP 배열의 lcp[i]는 suffix_array[i]와 suffix_array[i - 1]의 가장 긴 공통 접두사의 길이입니다.

"banana"의 접미사 배열과 LCP 배열은 아래와 같습니다.

| 접미사 | i   | lcp |
| ------ | --- | --- |
| a      | 6   | x   |
| ana    | 4   | 1   |
| anana  | 2   | 3   |
| banana | 1   | 0   |
| na     | 5   | 0   |
| nana   | 3   | 2   |

접미사 배열의 접미사를 단순 비교하여 LCP 배열을 구하게 되면, 시간복잡도는 O(N<sup>2</sup>)입니다. 하지만 몇 가지 아이디어를 이용하면 O(N)에 LCP 배열을 구할 수 있습니다.

### Python Code

```python
def get_lcp_array(string: str, suffix_array: list[int]) -> list[int]:
    string_length: int = len(string)
    prefix_length: int = 0
    lcp_array: list[int] = [0 for _ in range(string_length)]
    rank_list: list[int] = [0 for _ in range(string_length)]

    # rank_list에는 suffix array의 순서가 들어감
    # rank_list[index] = value
    # ---> string[index:]인 접미사는 suffix array의 value번째 위치함
    for idx, suffix in enumerate(suffix_array):
        rank_list[suffix] = idx

    for idx, rank in enumerate(rank_list):
        # suffix array의 첫 번째이면
        # (= 사전 순으로 나열했을 때, 첫 번째 접미사이므로 비교할 이전 접미사가 존재하지 않음)
        if rank == 0:
            continue

        jdx: int = suffix_array[rank - 1]

        while (
            idx + prefix_length < string_length
            and jdx + prefix_length < string_length
            and string[idx + prefix_length] == string[jdx + prefix_length]
        ):
            prefix_length += 1

        lcp_array[rank] = prefix_length

        if prefix_length > 0:
            prefix_length -= 1

    return lcp_array
```

<br>

## Reference

https://blog.myungwoo.kr/57

https://velog.io/@piopiop/Suffix-Array-%EB%A7%8C%EB%93%A4%EA%B8%B0-%ED%8C%8C%EC%9D%B4%EC%8D%AC

https://www.crocus.co.kr/614
