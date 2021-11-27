# 펜윅 트리(Fenwick Tree)

펜윅 트리는 BIT(Binary Indexed Tree)라고도 합니다.

<br>

## L[i]와 Tree[i]

펜윅 트리를 구현하려면, 어떤 수 X를 이진수를 나타냈을 때, 마지막 1의 위치를 알아야 합니다.

-   3 = 1**1**<sub>2</sub>
-   10 = 10**1**0<sub>2</sub>
-   12 = 1**1**00<sub>2</sub>

마지막 1이 나타내는 값을 L[i]라고 표현하겠습니다. L[3] = 1, L[10] = 2, L[12] = 4가 됩니다.

수 N개를 A[1] ~ A[N]이라고 했을 때, Tree[i]는 A[i]부터 이전 L[i]개의 합이 저장되어 있습니다.

아래 그림은 각각의 i에 대해서, L[i]를 나타낸 표입니다. 아래 초록 네모는 i부터 이전 L[i]개가 나타내는 구간입니다.

<p align='center'>
    <img src='https://user-images.githubusercontent.com/22341362/143672784-f547e323-f1a5-49a3-ba37-b4a5dd71639b.png' width='800' />
</p>

L[i] = i & -i 가 됩니다. 그 이유는 아래와 같습니다.

```
      -num = ~num + 1
       num = 10110000000
      ~num = 01001111111
      -num = 01010000000
num & -num = 00010000000
```

A = [3, 2, 5, 7, 10, 3, 2, 7, 8, 2, 1, 9, 5, 10, 7, 4]인 경우에, 각각의 Tree[i]가 저장하고 있는 값은 다음과 같습니다.

<p align='center'>
    <img src='https://user-images.githubusercontent.com/22341362/143672910-00df2ed7-f6ff-44e8-b21b-91aebb7c5765.png' width='800' />
</p>

Tree[12]에는 12부터 앞으로 L[12] = 4개의 합인 A[9] + A[10] + A[11] + A[12]가 저장되어 있습니다.

<br>

## 합 구하기

Tree를 이용해 A[1] + ... + A[13]은 어떻게 구할 수 있을까요?

13을 이진수로 나타내면 1101입니다. 따라서, A[1] + ... + A[13] = Tree[1101<sub>2</sub>] + Tree[1100<sub>2</sub>] + Tree[1000<sub>2</sub>]가 됩니다.

<p align='center'>
    <img src='https://user-images.githubusercontent.com/22341362/143673031-04d1fce7-255d-472d-b306-b95703d4cfc2.png' width='800' />
</p>

1101 --> 1100 --> 1000은 마지막 1의 위치를 빼면서 찾을 수 있습니다. 이것을 코드로 작성해보면 다음과 같습니다.

```python
def sum(i: int) -> int:
    ans: int = 0
    while i > 0:
        ans += tree[i]
        i -= (i & -i)
    return ans
```

어떤 구간의 합 A[i] + ... + A[j]는 A[1] + ... + A[j]에서 A[1] + ... + A[i - 1]을 뺀 값과 같습니다. 따라서, `sum(j) - sum(i - 1)`을 이용해서 구할 수 있습니다.

<br>

## 변경

어떤 수를 변경한 경우에는, 그 수를 담당하고 있는 구간을 모두 업데이트해줘야 합니다. 아래과 같이 마지막 1의 값을 더하는 방식으로 구현할 수 있습니다.

```python
def update(i: int, num: int) -> None:
    while i <= n:
        tree[i] += num
        i += (i & -i)
```

<br>

## Reference

https://www.acmicpc.net/blog/view/21
