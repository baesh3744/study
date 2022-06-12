# 입력 잡기술

## 연속된 숫자 하나씩 입력받기

```python
>>> nums = list(map(int, input()))  # 123456
>>> nums
[1, 2, 3, 4, 5, 6]
```

<br>

## 여러 번 입력받기

-   for문을 통해 input()을 여러 번 실행해야 하는 경우, input()으로 인한 시간 초과가 발생할 수 있다.
-   input() 대신 sys.stdin.readline() 사용한다.
-   sys.stdin.readline()은 한 줄 단위로 입력받기 때문에, 개행문자(/n)까지 입력받아진다.

```python
>>> import sys
>>> a, b = map(int, sys.stdin.readline().split())  # 1 2
```

<br>

### sys.stdin.readline()과 \n

```python
>>> num = int(sys.stdin.readline())
>>> nums = map(int, sys.stdin.readline().split())
```

와 같이 타입을 명시해주면 strip()을 사용해줄 필요는 없다. 단, str은 타입을 명시해줘도 \n이 붙어서 저장된다.

```python
>>> word = sys.stdin.readline().strip()
>>> words = [sys.stdin.readline().strip() for _ in range(n)]
```

와 같이 str으로 사용하려면 strip()을 사용해주어야 한다.

```python
>>> word = [sys.stdin.readline() for _ in range(2)]
# abc
# def
>>> word
['abc\n', 'def\n']
```

와 같이 str으로 사용하는데 strip()을 사용하지 않으면 \n까지 붙어서 저장된다.
