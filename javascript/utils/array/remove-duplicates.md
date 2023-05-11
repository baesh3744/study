# removeDuplicates()

> 중복되는 아이템을 제거한 배열을 반환한다.

## 1. Set

```ts
function removeDuplicates(array: any[]) {
  return [...new Set(array)];
}
```

## 2. indexOf + filter

```ts
function removeDuplicates(array: any[]) {
  return array.filter((element, index) => {
    return array.indexOf(element) === index;
  });
}
```

### 설명

- 중복되는 아이템은 index와 `indexOf()`의 결과값이 다르다.

```ts
const chars = ['A', 'B', 'A', 'C', 'B'];

chars.forEach((element, index) => {
  console.log(`${element} - ${index} - ${chars.indexOf(element)}`);
});

// Output:
//   A - 0 - 0
//   B - 1 - 1
//   A - 2 - 0 ✓
//   C - 3 - 3
//   B - 4 - 1 ✓
```

## 3. includes + forEach

```ts
function removeDuplicates(array: any[]) {
  const uniqueArray = [];

  array.forEach((element) => {
    if (!uniqueArray.includes(element)) {
      uniqueArray.push(element);
    }
  });

  return uniqueArray;
}
```
