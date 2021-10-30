# 클로저(Closure)

## Lexical Scoping

Lexical이란 lexical scoping 과정에서 변수가 어디에서 사용 가능한지 알기 위해 그 변수가 소스코드 내 어디에서 선언되었는지 고려한다는 것을 의미합니다. 중첩된 함수는 외부 스코프에서 선언한 변수에도 접근할 수 있습니다.

```javascript
function init() {
    var name = "Mozilla"; // name은 init에 의해 생성된 지역 변수입니다.
    // displayName()은 내부 함수이며, 클로저입니다.
    function displayName() {
        alert(name); // 부모 함수에서 선언된 변수를 사용합니다.
    }
    displayName();
}
init();
```

`init()`은 지역 변수 `name`과 함수 `displayName()`을 생성합니다. `displayName()`은 `init()` 안에 정의된 내부 함수이며 `init()` 함수 본문에서만 사용할 수 있습니다. 여기서 주의할 점은 `displayName()` 내부엔 자신만의 지역 변수가 없다는 점입니다. 그런데 함수 내부에서 외부 함수의 변수에 접근할 수 있기 때문에 `displayName()` 역시 부모 함수 `init()`에서 선언된 변수 `name`에 접근할 수 있습니다. 만약 `displayName()`가 자신만의 `name` 변수를 가지고 있었다면, `name` 대신 `this.name`을 사용했을 것입니다.

<br>

## 클로저

```javascript
function makeFunc() {
    var name = "Mozilla";
    function displayName() {
        alert(name);
    }
    return displayName;
}

var myFunc = makeFunc();
myFunc();
```

이 코드는 바로 전의 예제와 동일한 결과가 실행됩니다. 하지만 `displayName()` 함수가 실행되기 전에 외부 함수인 `makeFunc()`로부터 리턴되어 `myFunc` 변수에 저장된다는 차이가 있습니다.

JavaScript에서 함수는 클로저를 생성합니다. 클로저는 함수와 함수가 선언된 lexical 환경의 조합입니다. 이 환경은 클로저가 생성된 시점의 유효 범위 내에 있는 모든 지역 변수로 구성됩니다. `myFunc`은 `makeFunc()`이 실행 될 때 생성된 `displayName()` 함수의 인스턴스에 대한 참조입니다. `displayName()` 함수의 인스턴스는 변수 `name`이 있는 lexical 환경에 대한 참조를 유지합니다. 이런 이유로 `myFunc`가 호출될 때 변수 `name`은 사용할 수 있는 상태로 남게 되고 "Mozilla"가 `alert()`에 전달됩니다.

```javascript
function makeAdder(x) {
    var y = 1;
    return function (z) {
        y = 100;
        return x + y + z;
    };
}

// 클로저에 x와 y의 환경이 저장됩니다.
var add5 = makeAdder(5);
var add10 = makeAdder(10);

// 함수 실행 시, 클로저에 저장된 x, y 값에 접근하여 값을 계산합니다.
console.log(add5(2)); // 107 (x:5 + y:100 + z:2)
console.log(add10(2)); // 112 (x:10 + y:100 + z:2)
```

`add5`와 `add10`은 둘 다 클로저입니다. 이들은 같은 함수 본문 정의를 공유하지만 서로 다른 lexical 환경을 저장합니다. 함수 실행 시 `add5`의 lexical 환경에서 클로저 내부의 x는 5이지만 `add10`의 lexical 환경에서 x는 10입니다. 또한 리턴되는 함수에서 초기값이 1로 할당된 y에 접근하여 y 값을 100으로 변경한 것을 볼 수 있습니다. 이는 클로저가 리턴된 후에도 외부 함수의 변수들에 접근이 가능하다는 것을 보여주며 클로저에 단순히 값 형태로 전달되는 것이 아니라는 것을 의미합니다.

<br>

## 실용적인 클로저

클로저는 어떤 데이터(lexical 환경)와 그 데이터를 조작하는 함수를 연관시켜주기 때문에 유용합니다. 이것은 객체가 어떤 데이터(그 객체의 속성)와 하나 혹은 그 이상의 메소드들을 연관시키는 객체지향 프로그래밍과 같은 맥락에 있습니다.

결과적으로 오직 하나의 메소드를 가지고 있는 객체를 사용하는 모든 곳에 클로저를 사용할 수 있습니다.

이렇게 할 수 있는 상황은 특히 웹에서 일반적입니다. Front-end JavaScript에서 우리가 쓰는 많은 코드가 이벤트 기반입니다. 우리는 몇 가지 동작을 정의한 다음 사용자에 의한 이벤트에 연결합니다. 우리의 코드는 일반적으로 callback (이벤트에 응답하여 실행되는 단일 함수) 으로 첨부됩니다.

### 예시

```javascript
function makeSizer(size) {
    return function () {
        document.body.style.fontSize = size + "px";
    };
}

var size12 = makeSizer(12);
var size14 = makeSizer(14);

document.getElementById("size-12").onclick = size12;
document.getElementById("size-14").onclick = size14;
```

<br>

## 클로저를 이용해서 private method 흉내내기

Java와 같은 몇몇 언어들은 메소드를 private으로 선언할 수 있는 기능을 제공합니다. 이는 같은 클래스 내부의 다른 메소드에서만 그 메소드들을 호출할 수 있다는 의미입니다. Private method는 코드에 제한적인 접근만을 허용한다는 점 뿐만 아니라 전역 네임 스페이스를 관리하는 강력한 방법을 제공하여 불필요한 메소드가 공용 인터페이스를 혼란스럽게 만들지 않도록 합니다.

JavaScript는 태생적으로 이런 방법을 제공하지 않지만 클로저를 이용하여 private method를 흉내내는 것이 가능합니다.

```javascript
var makeCounter = function () {
    var privateCounter = 0;
    function changeBy(val) {
        privateCounter += val;
    }
    return {
        increment: function () {
            changeBy(1);
        },
        decrement: function () {
            changeBy(-1);
        },
        value: function () {
            return privateCounter;
        },
    };
};

var counter1 = makeCounter();
var counter2 = makeCounter();

console.log(counter1.value()); // 0
counter1.increment();
counter1.increment();
console.log(counter1.value()); // 2
counter1.decrement();
console.log(counter1.value()); // 1
console.log(counter2.value()); // 0
```

우리는 `counter.increment`, `counter.decrement`, `counter.value` 세 함수에 의해 공유되는 하나의 lexical 환경을 만듭니다.

공유되는 lexical 환경은 `makeCounter()` 함수를 통해 만들어집니다. 이 lexical 환경은 두 개의 private 아이템을 포함합니다. 하나는 `privateCounter`라는 변수이고 나머지 하나는 `changeBy()`라는 함수입니다. 둘 다 `makeCounter()` 함수 외부에서 접근할 수 없습니다. 대신 반환된 세 개의 public 함수를 통해서만 접근할 수 있습니다.

각 클로저는 그들 고유의 클로저를 통해 `privateCounter` 변수의 다른 버전을 참조합니다. 각 카운터가 호출될 때마다, 하나의 클로저에서 변수 값을 변경해도 다른 클로저의 값에는 영향을 주지 않습니다.

이런 방식으로 클로저를 사용하여 객체지향 프로그래밍의 정보 은닉(data hiding)과 캡슐화(encapsulation) 같은 이점들을 얻을 수 있습니다.

<br>

## 클로저 스코프 체인

모든 클로저에는 세 가지 스코프(범위)가 있습니다.

-   지역 스코프(lo스코프, 스코프)
-   외부 함수 스코프(outer functi스코프)
-   전역 스코프(glo스코프)

여러 번 중첩된 함수가 존재할 때, 내부 함수는 바로 위 단계의 외부 함수 뿐만 아니라 내부 함수를 감싸고 있는 모든 외부 함수 스코프에 접근할 수 있습니다.

```javascript
// glo스코프
var e = 10;
function sum(a) {
    return function (b) {
        return function (c) {
            // outer functi스코프
            return function (d) {
                // lo스코프
                return a + b + c + d + e;
            };
        };
    };
}

console.log(sum(1)(2)(3)(4)); // 20
```

<br>

## 성능 관련 고려 사항

특정 작업에 클로저가 필요하지 않는데 다른 함수 내에서 함수를 불필요하게 작성하는 것은 처리 속도와 메모리 소비 측면에서 부정적인 영향을 미칠 것입니다.

예를 들어, 새로운 객체/클래스를 생성 할 떄, 메소드는 일반적으로 객체 생성자에 정의되기보다는 객체의 프로토타입에 연결되어야 합니다. 그 이유는 메소드를 객체 생성자에 정의하면 생성자가 호출 될 때마다 메소드가 다시 할당되지만, 메소드를 객체의 프로토타입에 연결하면 상속된 프로토타입은 모든 객체에서 공유될 수 있으며 메소드 정의는 모든 객체 생성 시 발생할 필요가 없습니다.

```javascript
// before
// 클로저의 이점을 이용하지 않은 예
function MyObject(name, message) {
    this.name = name.toString();
    this.message = message.toString();
    this.getName = function () {
        return this.name;
    };
    this.getMessage = function () {
        return this.message;
    };
}

// after
// 기존 프로토타입에 추가
function MyObject(name, message) {
    this.name = name.toString();
    this.message = message.toString();
}
MyObject.prototype.getName = function () {
    return this.name;
};
MyObject.prototype.getMessage = function () {
    return this.message;
};

// 좀 더 꺠끗한 방법
function MyObject(name, message) {
    this.name = name.toString();
    this.message = message.toString();
}
(function () {
    this.getName = function () {
        return this.name;
    };
    this.getMessage = function () {
        return this.message;
    };
}.call(MyObejct.prototype));
```

<br>

## Reference

https://developer.mozilla.org/ko/docs/Web/JavaScript/Closures
