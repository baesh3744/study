# 배열 -> 문자열

## 문자열 배열 -> 문자열

```
>> mylist = ['h', 'e', 'l', 'l', 'o']
>> ''.join(mylist)
hello
```

<br>

## int 배열 -> 문자열

```
>> mylist = [1, 2, 3, 4, 5, 6]
>> ''.join(map(str, mylist))
123456
>> ' '.join(map(str, mylist))
1 2 3 4 5 6
```
