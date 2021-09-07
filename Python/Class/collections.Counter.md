# collections.Counter

## Dictionary를 이용한 카운팅

```python
def count_word(word: str) -> dict[str, int]:
    counter: dict[str, int] = {}
    for char in word:
        if char in counter:
            counter[char] += 1
        else:
            counter[char] = 1
    return counter

count_word('hello world')
# {'h': 1, 'e': 1, 'l': 3, 'o': 2, ' ': 1, 'w': 1, 'r': 1, 'd': 1}
```

<br>

## collections.Counter를 이용한 카운팅

```python
>>> from collections import Counter
>>> Counter('hello world')
Counter({'l': 3, 'o': 2, 'h': 1, 'e': 1, ' ': 1, 'w': 1, 'r': 1, 'd': 1})
```

-   Counter는 입력값의 빈도를 Dictionary 형태로 리턴한다. 리턴값에는 Dictionary API를 적용할 수 있다.
-   Counter의 입력값으로 가능한 것들
    -   리스트 (List)
    -   딕셔너리 (Dictionary)
    -   '값=개수' 형태
    -   문자열 (String)

<br>

### elements()

```python
>>> counter = Counter('hello world')
>>> list(c.elements())
['h', 'e', 'l', 'l', 'l', 'o', 'o', ' ', 'w', 'r', 'd']
>>> sorted(c.elements())
[' ', 'd', 'e', 'h', 'l', 'l', 'l', 'o', 'o', 'r', 'w']
```

<br>

### most_common()

```python
>>> counter = Counter('hello world')
>>> counter.most_common()
[('l', 3), ('o', 2), ('h', 1), ('e', 1), (' ', 1), ('w', 1), ('r', 1), ('d', 1)]
>>> counter.most_common(1)
[('l', 3)]
```

-   most_common()는 빈도순으로 정렬된 리스트를 리턴해준다.
-   most_common(n)는 빈도순으로 정렬된 리스트에서 첫 n개만 보여준다.

<br>

## Reference

https://docs.python.org/3/library/collections.html#collections.Counter
