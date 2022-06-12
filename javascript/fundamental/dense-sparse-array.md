# 밀집 배열(Dense Array)와 희소 배열(Sparse Array)

## 밀집 배열

밀집 배열은 "일반적으로" 알고 있는 배열을 의미합니다. 밀집 배열은 인덱스 0부터 순차적으로 원소를 채워갑니다.

```javascript
let array = [1, 2, 3, 4, 5];
console.log(array.length); // 5
```

<br>

## 희소 배열

희소 배열은 인덱스 사이에 구멍이 존재할 수 있어, 원소들이 순차적으로 존재하지 않는 배열을 말합니다.

```javascript
let array = [];
array[100] = "Holes now exist";
console.log(array.length); // 101, `length` 프로퍼티는 배열의 개수가 아닌 마지막 인덱스 + 1을 반환합니다.
```

### 희소 배열의 생성

#### 1. Array literal

```javascript
const names = ["Batman", , "Bane"];
console.log(names); // ['Batman', empty, 'Bane']
```

#### 2. `Array()` constructor

```javascript
const array = Array(3);
console.log(array); // [empty, empty, empty]
```

#### 3. `delete` operator

```javascript
const names = ["Batman", "Joker", "Bane"];

delete names[1];

console.log(names); // ['Batman', empty, 'Bane']
```

#### 4. Increase `length` property

```javascript
const names = ["Batman", "Joker", "Bane"];

names.length = 5;

console.log(names); // ['Batmna', 'Joker', 'Bane', empty, empty]
```

### 희소 배열과 빌트인 메소드

대부분의 빌트인 메소드는 희소 배열의 구멍을 스킵합니다.

```javascript
const names = ["Batman", , "Bane"];

names.forEach((name) => {
    console.log(name);
});
// 'Batman'
// 'Bane'
```

<br>

## Reference

-   [Kealan Parr - "Sparse Arrays vs Dense Arrays in JavaScript - Explained with Examples"](https://www.freecodecamp.org/news/sparse-and-dense-arrays-in-javascript/)
-   [Dmitri Pavlutin - "Sparse vs Dense Arrays in JavaScript"](https://dmitripavlutin.com/javascript-sparse-dense-arrays/)
