# Mutable vs immutable

출처

https://wikidocs.net/16038

| class     | 설명                                     | 구분      |
| --------- | ---------------------------------------- | --------- |
| list      | mutable한 순서가 있는 객체 집합          | mutable   |
| set       | mutable한 순서가 없는 고유한 객체 집합   | mutable   |
| dict      | key와 value가 mapping된 객체 (순서 없음) | mutable   |
| bool      | 참 or 거짓                               | immutable |
| int       | 정수                                     | immutable |
| float     | 실수                                     | immutable |
| tuple     | immutable한 순서가 있는 객체 집합        | immutable |
| str       | 문자열                                   | immutable |
| frozenset | immutable한 set                          | immutable |

-   mutable은 값을 변경해도 메모리 주소는 동일하다.
-   immutable은 값의 변경을 시도하면 에러가 발생한다.
-   값의 재할당은 mutable/immutable과는 다른 문제이다.

<br />

<br />

# 얕은 복사(Shallow Copy) vs 깊은 복사(Deep Copy)

## 얕은 복사(Shallow Copy) - slicing, copy.copy()

---

```
>> a = [1, 2, 3]
>> b = a  # shallow copy

>> b[0]= 5
>> a
[5, 2, 3]
>> b
[5, 2, 3]

>> id(a)
4396179528
>> id(b)
4396179528
```

b = a 는 b에 값이 할당되는 것이 아니라 b가 a의 메모리 주소를 바라본다. 따라서, b를 변경하면 a도 같이 변경된다.

```
>> a = [1,2,3]
>> b = a[:]

>> id(a)
4396179528
>> id(b)
4393788808

>> a == b
True
>> a is b
False

>> b[0] = 5
>> a
[1, 2, 3]
>> b
[5, 2, 3]
```

slicing을 통해 값을 할당하면 새로운 id가 부여되며, 서로 영향을 받지 않는다.

mutable 객체 안에 mutable 객체인 경우 문제가 된다.

```
>> a = [[1,2], [3,4]]
>> b = a[:]

>> id(a)
4395624328
>> id(b)
4396179592

>> id(a[0])
4396116040
>> id(b[0])
4396116040
```

id(a) 값과 id(b) 값은 다르지만, 내부의 객체 id(a[0])과 id(b[0])은 같은 주소를 바라보고 있다.

```
>> a[0] = [8,9]
>> a
[[8, 9], [3, 4]]
>> b
[[1, 2], [3, 4]]

>> id(a[0])
4393788808
>> id(b[0])
4396116040
```

재할당하는 경우는 문제가 없다. 메모리 주소도 변경되었다.

```
>> a[1].append(5)
>> a
[[8, 9], [3, 4, 5]]
>> b
[[1, 2], [3, 4, 5]]

>> id(a[1])
4396389896
>> id(b[1])
4396389896
```

하지만, a[1]에 값을 변경하면, b[1]도 따라서 변경된다.

<br />

<br />

## 깊은 복사 (deep copy) - copy.deepcopy()

---

내부의 객체들까지 모두 새롭게 복사되는 것이다.

```
>> import copy

>> a = [[1,2],[3,4]]
>> b = copy.deepcopy(a)

>> a[1].append(5)
>> a
[[1, 2], [3, 4, 5]]
>> b
[[1, 2], [3, 4]]
```
