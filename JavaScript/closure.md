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

-   지역 스코프(local 스코프, 스코프)
-   외부 함수 스코프(outer function 스코프)
-   전역 스코프(global 스코프)

여러 번 중첩된 함수가 존재할 때, 내부 함수는 바로 위 단계의 외부 함수 뿐만 아니라 내부 함수를 감싸고 있는 모든 외부 함수 스코프에 접근할 수 있습니다.

```javascript
// global 스코프
var e = 10;
function sum(a) {
    return function (b) {
        return function (c) {
            // outer function 스코프
            return function (d) {
                // local 스코프
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

## 클로저의 활용

### 1. 상태 유지

클로저가 가장 유용하게 사용되는 상황은 현재 상태를 기억하고 변경된 최신 상태를 유지하는 것입니다.

```html
<!DOCTYPE html>
<html>
    <body>
        <button class="toggle">toggle</button>
        <div
            class="box"
            style="width: 100px; height: 100px; background: red;"
        ></div>

        <script>
            var box = document.querySelector(".box");
            var toggleBtn = document.querySelector(".toggle");

            var toggle = (function () {
                var isShow = false;

                // ① 클로저를 반환
                return function () {
                    box.style.display = isShow ? "block" : "none";
                    // ③ 상태 변경
                    isShow = !isShow;
                };
            })();

            // ② 이벤트 프로퍼티에 클로저를 할당
            toggleBtn.onclick = toggle;
        </script>
    </body>
</html>
```

① IIFE는 함수를 반환하고 즉시 소멸합니다. IIFE가 반환한 함수는 자신이 생성되었을 때의 렉시컬 환경(lexical environment)에 속한 변수 `isShow`를 기억하는 클로저입니다. 클로저가 기억하는 변수 `isShow`는 box 요소의 표시 상태를 나타냅니다.
② 클로저를 이벤트 핸들러로서 이벤트 프로퍼티에 할당합니다. 이벤트 프로퍼티에서 이벤트 핸들러인 클로저를 제거하지 않는 한 클로저가 기억하는 렉시컬 환경의 변수 `isShow`는 소멸하지 않습니다. 다시 말해 현재 상태를 기억합니다.
③ 버튼을 클릭하면 이벤트 프로퍼티에 할당한 이벤트 핸들러인 클로저가 호출됩니다. 이때, box 요소의 표시 상태를 나타내는 변수 `isShow`의 값이 변경됩니다. 변수 `isShow`는 클로저에 의해 참조되고 있기 때문에 유효하며 자신의 변경된 최신 상태를 계속해서 유지합니다.

이처럼 클로저는 현재 상태 (위 예제의 경우 `isShow` 변수) 를 기억하고 이 상태가 변경되어도 최신 상태를 유지해야 하는 상황에 매우 유용합니다. 만약 JavaScript에 클로저라는 기능이 없다면, 최신 상태를 유지하기 위해 전역 변수를 사용할 수 밖에 없습니다. 전역 변수는 언제든지 누구나 접근할 수 있고 변경할 수 있기 때문에 많은 부작용을 유발해 오류의 원인이 되므로 사용을 억제해야 합니다.

### 2. 전역 변수의 사용 억제

버튼이 클릭될 때마다 클릭한 횟수가 누적되어 화면에 표시되는 카운터를 만들어 봅시다.

```html
<!DOCTYPE html>
<html>
    <body>
        <button id="inclease">+</button>
        <p id="count">0</p>

        <script>
            var incleaseBtn = document.getElementById("inclease");
            var count = document.getElementById("count");

            // 카운트 상태를 유지하기 위한 전역 변수
            var counter = 0;

            function increase() {
                return ++increase;
            }

            incleaseBtn.onclick = function () {
                count.innerHTML = increase();
            };
        </script>
    </body>
</html>
```

위 코드는 잘 동작하지만 오류를 발생시킬 가능성을 내포하고 있는 좋지 않은 코드입니다. `increase()` 함수는 호출되기 직전에 전역변수 `counter`의 값이 반드시 0이어야 제대로 동작합니다. 하지만 변수 `counter`는 전역 변수이기 때문에 언제든지 누구나 접근할 수 있고 변경할 수 있습니다. 만약 누군가에 의해 의도치 않게 전역 변수 `counter`의 값이 변경됐다면, 이는 오류로 이어집니다. 변수 `counter`는 카운터를 관리하는 `increase()` 함수가 관리하는 것이 바람직합니다.

```html
<!DOCTYPE html>
<html>
    <body>
        <button id="inclease">+</button>
        <p id="count">0</p>

        <script>
            var incleaseBtn = document.getElementById("inclease");
            var count = document.getElementById("count");

            function increase() {
                // 카운트 상태를 유지하기 위한 지역 변수
                var counter = 0;
                return ++increase;
            }

            incleaseBtn.onclick = function () {
                count.innerHTML = increase();
            };
        </script>
    </body>
</html>
```

전역 변수를 지역 변수로 변경하여 의도치 않은 상태 변경은 방지했습니다. 하지만 `increase()` 함수가 호출될 때마다 변수 `counter`를 0으로 초기화하기 때문에 언제나 1이 표시됩니다. 즉, 변경된 이전 상태를 기억하지 못합니다.

```html
<!DOCTYPE html>
<html>
    <body>
        <button id="inclease">+</button>
        <p id="count">0</p>

        <script>
            var incleaseBtn = document.getElementById("inclease");
            var count = document.getElementById("count");

            var increase = (function () {
                // 카운트 상태를 유지하기 위한 자유 변수
                var counter = 0;
                // 클로저를 반환
                return function () {
                    return ++counter;
                };
            })();

            incleaseBtn.onclick = function () {
                count.innerHTML = increase();
            };
        </script>
    </body>
</html>
```

스크립트가 실행되면 IIFE가 호출되고 변수 `increase`에는 함수 `function () { return ++counter; }`가 할당됩니다. 이 함수는 자신이 생성됐을 때의 렉시컬 환경을 기억하는 클로저입니다. IIFE는 호출된 이후 소멸되지만, IIFE가 반환한 함수는 변수 `increase`에 할당되어 `inclease` 버튼을 클릭하면 클릭 이벤트 핸들러 내부에서 호출됩니다. 이때 클로저인 이 함수는 자신이 선언됐을 때의 렉시컬 환경인 IIFE의 스코프에 속한 지역 변수 `counter`를 기억합니다. 따라서 IIFE의 변수 `counter`에 접근할 수 있고 변수 `counter`는 자신을 참조하는 함수가 소멸될 때까지 유지됩니다.

IIFE는 한 번만 실행되므로 `increase`가 호출될 때마다 변수 `counter`가 재차 초기화될 일은 없을 것입니다. 변수 `counter`는 외부에서 직접 접근할 수 없는 `private` 변수이므로 전역 변수를 사용했을 때와 같이 의도되지 않은 변경을 걱정할 필요도 없기 때문에 보다 안정적인 프로그래밍이 가능합니다.

변수의 값은 누군가에 의해 언제든지 변경될 수 있어 오류 발생의 근본적인 원인이 될 수 있습니다. 상태 변경이나 가변(mutable) 데이터를 피하고 불변성(immutability)를 지향하는 함수형 프로그래밍에서 부수 효과(side effect)를 최대한 억제하여 오류를 피하고 프로그램의 안정성을 높이기 위해 클로저는 적극적으로 사용됩니다.

```javascript
// 함수를 인자로 전달박도 함수를 반환하는 고차 함수
// 이 함수가 반환하는 함수는 클로저로서 카운트 상태를 유지하기 위한 자유 변수 counter를 기억합니다.
function makeCounter(predicate) {
    // 카운트 상태를 유지하기 위한 자유 변수
    var counter = 0;
    // 클로저를 반환
    return function () {
        counter = predicate(counter);
        return counter;
    };
}

// 보조 함수
function increase(n) {
    return ++n;
}

// 보조 함수
function decrease(n) {
    return --n;
}

// 함수로 함수를 생성합니다.
// makeCounter 함수는 보조 함수를 인자로 전달받아 함수를 반환합니다.
const increaser = makeCounter(increase);
console.log(increaser()); // 1
console.log(increaser()); // 2

// increaser 함수와는 별개의 독립된 렉시컬 환경을 갖기 때문에 카운터 상태를 연동하지 않습니다.
const decreaser = makeCounter(decrease);
console.log(decreaser()); // -1
console.log(decreaser()); // -2
```

함수 `makeCounter()`는 보조 함수를 인자로 전달받고 함수를 반환하는 고차 함수입니다. 함수 `makeCounter()`가 반환하는 함수는 자신이 생성됐을 때의 렉시컬 환경인 함수 `makeCounter()`의 스코프에 속한 변수 `counter`를 기억하는 클로저입니다. 함수 `makeCounter()`는 인자로 전달받은 보조 함수를 합성하여 자신이 반환하는 함수의 동작을 변경할 수 있습니다. 이때 주의해야 할 것은 함수 `makeCounter()`를 호출해 함수를 반환된 함수는 자신만의 독립된 렉시컬 환경을 갖는다는 것입니다. 이는 함수를 호출하면 그때마다 새로운 렉시컬 환경이 생성되기 때문입니다. 위 예제에서 변수 `increaser`와 변수 `decreaser`에 할당된 함수는 각각 자신만의 독립된 렉시컬 환경을 갖기 때문에 카운트를 유지하기 위한 자유 변수 `counter`를 공유하지 않아 카운터의 증감이 연동하지 않습니다. 따라서 독립된 카운터가 아니라 연동하여 증감이 가능한 카운터를 만들려면 렉시컬 환경을 공유하는 클로저를 만들어야 합니다.

### 3. 정보의 은닉

생성자 함수 `Counter`를 생성하고 이를 통해 counter 객체를 만들어 봅시다.

```javascript
function Counter() {
    // 카운트를 유지하기 위한 자유 변수
    var counter = 0;

    // 클로저
    this.increase = function () {
        return ++counter;
    };

    // 클로저
    this.decrease = function () {
        return --counter;
    };
}

const counter = new Counter();

console.log(counter.increase()); // 1
console.log(counter.decrease()); // 0
```

생성자 함수 `Counter()`는 `increase()`, `decrease()` 메소드를 갖는 인스턴스를 생성합니다. 이 메소드들은 모두 자신이 생성됐을 때의 렉시컬 환경인 생성자 함수 `Counter()`의 스코프에 속한 변수 `counter`를 기억하는 클로저이며 렉시컬 환경을 공유합니다. 생성자 함수가 생성한 객체의 메소드는 객체의 프로퍼티에만 접근할 수 있는 것이 아니며 기억하는 렉시컬 환경의 변수에도 접근할 수 있습니다.

이때 생성자 함수 `Counter()`의 변수 `counter`는 this에 바인딩된 프로퍼티가 아니라 변수입니다. `counter`가 this에 바인딩된 프로퍼티라면 생성자 함수 `Counter()`가 생성한 인스턴스를 통해 외부에서 접근이 가능한 `public` 프로퍼티가 되지만 생성자 함수 `Counter()` 내에서 선언된 변수 `counter`는 생성자 함수 `Counter()` 외부에서 접근할 수 없습니다. 하지만 생성자 함수 `Counter()`가 생성한 인스턴스의 메소드인 `increase()`, `decrease()`는 클로저이기 때문에 자신이 생성됐을 때의 렉시컬 환경인 생성자 함수 `Counter()`의 변수 `counter`에 접근할 수 있습니다. 이러한 클로저의 특징을 사용해 클래스 기반 언어의 `private` 키워드를 흉내낼 수 있습니다.

<br>

## Reference

-   [MDN Web Docs - "클로저"](https://developer.mozilla.org/ko/docs/Web/JavaScript/Closures)
-   [PoiemaWeb - "5.19 클로저"](https://poiemaweb.com/js-closure)
