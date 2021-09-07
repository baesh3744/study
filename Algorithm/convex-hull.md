# 볼록 껍질 (Convex Hull)

## 볼록 껍질 (convex hull) 이란

-   2차원 평면 상에 점이 여러 개가 있을 때, 이 점들 중 일부를 골라서 만들 수 있는, 나머지 점들을 모두 포함하는 볼록다각형의 경계를 말한다.
-   이 때, 포함한다는 말은 다각형의 변 위에 점이 존재하는 것도 인정한다.
-   이러한 다각형이 항상 볼록다각형으로 결정될 수 있는가?
    -   점을 모두 포함하는 어떤 오목다각형이 있다고 해도, 오목한 지점의 점을 다각형에서 제외시키고 제외시킨 점의 전후 점을 이어버리면 새 다각형에 그 점도 포함되고, 새 다각형은 볼록다각형이 된다.

<br>

## Convex Hull 알고리즘의 종류

-   Jarvis march
    -   점의 개수 N개와 볼록 껍질을 이루는 점의 개수 H개에 대해 O(NH)의 시간 복잡도를 갖는다.
-   Graham's scan
    -   점 하나를 기준으로 뽑아서 그 점을 기준으로 시계방향 또는 반시계방향으로 나머지 점들을 훑어가며 볼록 껍질에의 포함 여부를 결정한다.
    -   점을 정렬하는 시간에 영향을 받아 O(NlogN)의 시간 복잡도를 갖는다.
-   Monotone chain
    -   볼록 껍질을 윗 껍질, 아래 껍질로 나누어 구한 뒤, 합친다. 이 때, 나누어지는 윗 껍질과 아래 껍질은 시작점과 끝점을 공유한다.

<br>

## Graham's scan 알고리즘의 동작

1. 점 하나를 기준으로 뽑는다. 보통 y좌표가 가장 작은 점을 선택한다.
2. 그 점을 기준으로 시계방향 또는 반시계방향으로 정렬한다.
3. 기준점(0번)과 기준점 옆의 첫 번째 점(1번)을 스택에 쌓는다.
4. 나머지 점들을 훑어가며 볼록 껍질에의 포함 여부를 결정한다.
    - 스택에서 위의 점 2개가 이루는 직선을 기준으로 점들이 항상 한쪽 방향에만 나타나게 한다.
    - 만약, 다음 점이 스택에서 위의 점 2개가 이루는 직선과 이루는 방향이 이전과 반대라면, 스택에서 제일 위에 위치한 점을 빼고 다음 점을 쌓는다. 이 때, 빼야할 점이 여러 개 존재할 수도 있다.

<br>

## Monotone chain 알고리즘의 동작

1. 점들을 한 축 (x축 또는 y축) 에 대해 정렬한다.
2. 윗 껍질을 구하는 경우, Graham's scan처럼 스택을 하나 만든 뒤, CCW 방향으로 점들을 삽입해준다.
3. 아래 껍질을 구하는 경우, 정렬한 점들을 뒤집어서 마찬가지로 CCW 방향으로 스택에 삽입해준다.
4. 윗 껍질과 아래 껍질을 합친다.

<br>

## Python Code

```python
Point = tuple[int, int]


def ccw(a: Point, b: Point, c: Point) -> int:
    op: int = a[0] * b[1] + b[0] * c[1] + c[0] * a[1]
    op -= a[1] * b[0] + b[1] * c[0] + c[1] * a[0]
    return op


def monotone_chain(dot_list: list[Point]) -> tuple[list[Point], list[Point]]:
    upper: list[Point] = []
    lower: list[Point] = []

    dot_list.sort()
    for dot in dot_list:
        while len(upper) > 1 and ccw(upper[-2], upper[-1], dot) <= 0:
            upper.pop()
        while len(lower) > 1 and ccw(lower[-2], lower[-1], dot) >= 0:
            lower.pop()
        upper.append(dot)
        lower.append(dot)
    return upper, lower
```

<br>

## Reference

https://m.blog.naver.com/PostView.naver?isHttpsRedirect=true&blogId=kks227&logNo=220857597424
https://atez.kagamine.me/43
