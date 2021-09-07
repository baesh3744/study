# 거듭제곱

## 1. \*\*

-   가장 빠르다.

<br>

## 2. pow(base, exp[, mod])

-   pow(base, exp)는 base \*\* exp와 같은 기능을 한다.
-   세 번째 인자 mod를 입력하면, base와 exp는 정수형, exp는 양수이어야 한다.
-   pow(base, exp, mod)를 사용하는 것이 pow(base, exp) % mod로 계산하는 것 보다 효율적이다.

<br>

## 3. math.pow(base, exp)

-   두 인자를 float형으로 바꾸고 float형 결과를 반환한다.

<br>

## 결론

-   단순한 거듭제곱 연산을 원한다면, \*\*
-   거듭제곱 후 mod 연산을 원한다면, pow()
-   인자에 상관없이 float형 결과를 원한다면, math.pow()
