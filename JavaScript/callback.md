# 콜백(Callback)

JavaScript 호스트 환경이 제공하는 여러 함수를 사용하면 비동기(asynchronous) 동작을 스케줄링 할 수 있습니다. 원하는 때에 동작이 시작하도록 할 수 있는 것입니다.

`src`에 있는 스크립트를 읽어오는 함수 `loadScript(src)`를 예시로 비동기 동작 처리가 어떻게 일어나는지 살펴봅시다.

```javascript
function loadScript(src) {
    let script = document.createElement("script");
    script.src = src;
    document.head.append(script);
}
```

`loadScript(src)` 함수는 `<script src="...">`를 동적으로 만들고 이를 문서에 추가합니다. 브라우저는 자동으로 태그에 있는 스크립트를 불러오고, 로딩이 완료되면 스크립트를 실행합니다.

그런데 이 때 스크립트는 "비동기적으로" 실행됩니다. 로딩은 지금 당장 시작되더라도 실행은 함수가 끝난 후에야 되기 때문입니다.

따라서 `loadScript()` 아래에 있는 코드들은 스크립트 로딩이 종료되는 걸 기다리지 않습니다.

스크립트 로딩이 끝나자마자 이 스크립트를 사용해 무언가를 해야만 한다고 가정해 봅시다. 스크립트 안에 다양한 함수가 정의되어 있고, 우리는 이 함수를 실행하길 원하는 상황입니다. 그런데 `loadScript()`를 호출하자마자 내부 함수를 호출하면 원하는 대로 작동하지 않습니다.

```javascript
loadScript("/my/script.js"); // script.js 안에 `funcion newFunction() { ... }`이 있습니다.

newFunction(); // 함수가 존재하지 않는다는 에러가 발생합니다.
```

에러는 브라우저가 스크립트를 읽어올 수 있는 시간을 충분히 확보하지 못했기 때문에 발생합니다. 그런데 현재로서는 `loadScript()`에서 스크립트 로딩이 완료되었는지 알 방법이 없습니다.

`loadScript()`의 두 번째 인수로 스크립트 로딩이 끝난 후 실행될 함수인 콜백 함수를 추가해 봅시다.

```javascript
function loadScript(src) {
    let script = document.createElement("script");
    script.src = src;

    script.onload = () => callback(script);

    document.head.append(script);
}
```

스크립트에 있는 함수를 콜백 함수 안에서 호출하면 원하는 대로 외부 스크립트 안의 함수를 사용할 수 있습니다.

```javascript
loadScript("/my/script.js", function () {
    newFunction();
});
```

이렇게 두 번째 인수로 전달된 함수는 원하는 동작 (위 예제에서는 외부 스크립트를 불러오는 것) 이 완료되었을 때 실행됩니다.

이런 방식을 "콜백 기반(callbak-based)" 비동기 프로그래밍이라고 합니다. 무언가를 비동기적으로 수행하는 함수는 반드시 콜백 (함수 내 동작이 모두 처리된 후 실행되어야 하는 함수) 을 인수로 제공해야 합니다.

<br>

## 에러 핸들링

`loadScript()`에서 로딩 에러를 추적할 수 있게 기능을 개선해봅시다.

```javascript
function loadScript(src, callback) {
    let script = document.createElement("script");
    script.src = src;

    srcipt.onload = () => callback(null, script);
    script.onerror = () =>
        callback(new Error(`${src}를 불러오는 도중에 에러가 발생했습니다.`));

    document.head.append(script);
}
```

이제 `loadScript()`는 스크립트 로딩에 성공하면 `callback(null, script)`를, 실패하면 `callback(error)`를 호출합니다.

개선된 `loadScript()`의 사용법은 다음과 같습니다.

```javascript
loadScript("/my/script.js", function (error, script) {
    if (error) {
        // 에러 처리
    } else {
        // 스크립트 로딩이 성공적으로 끝남
    }
});
```

이렇게 에러를 처리하는 방식은 흔히 사용되는 패턴입니다. 이런 패턴을 "오류 우선 콜백(error-first callback)"이라고 부릅니다.

오류 우선 콜백은 다음 관례를 따릅니다.

1. `callback()`의 첫 번째 인수는 에러를 위해 남겨둡니다. 에러가 발생하면 이 인수를 이용해 `callback(err)`가 호출됩니다.
2. 두 번째 인수 (필요하면 인수를 더 추가할 수 있음) 는 에러가 발생하지 않았을 때를 위해 남겨둡니다. 원하는 동작이 성공한 경우엔 `callback(null, result1, result2, ...)`가 호출됩니다.

<br>

## 콜백의 중첩

스크립트가 두 개 있는 경우, 어떻게 하면 두 스크립트를 순차적으로 불러올 수 있을까요? 가장 자연스러운 방법은 아래와 같이 콜백 함수 안에 두 번째 `loadScript()`를 호출하는 것입니다.

```javascript
loadScript("/my/script.js", function (error, script) {
    console.log(`첫 번째 스크립트 ${script.src}를 로딩했습니다.`);

    loadScript("/my/script2.js", function (error, script) {
        console.log(`두 번째 스크립트 ${script.src}를 로딩했습니다.`);
    });
});
```

### 멸망의 피라미드(Pyramid of Doom)

위와 같은 코딩 방식은 꼬리에 꼬리를 무는 비동기 동작이 많아지면 아래와 같은 코드 작성이 불가피해집니다.

```javascript
loadScript("1.js", function (error, script) {
    if (error) {
        handleError(error);
    } else {
        // ...

        loadScript("2.js", function (error, script) {
            if (error) {
                handleError(error);
            } else {
                // ...

                loadScript("3.js", function (error, script) {
                    if (error) {
                        handleError(error);
                    } else {
                        // ...
                    }
                });
            }
        });
    }
});
```

호출이 계속 중첩되면서 코드가 깊어지고 있습니다. 이렇게 깊은 중첩 코드가 만들어내는 패턴을 "콜백 지옥(callback hell)" 또는 "멸망의 피라미드(pyramid of doom)"이라고 부릅니다.

따라서 이런 코딩 방식은 좋지 않습니다. 각 동작을 독립적인 함수로 만들면 위와 같은 문제를 완화할 수 있습니다.

```javascript
loadScript("1.js", step1);

function step1(error, script) {
    if (error) {
        handleError(error);
    } else {
        // ...

        loadScript("2.js", step2);
    }
}

function step2(error, script) {
    if (error) {
        handleError(error);
    } else {
        // ...

        loadScript("3.js", step3);
    }
}

function step3(error, script) {
    if (error) {
        handleError(error);
    } else {
        // ...
    }
}
```

새롭게 작성한 코드는 각 동작을 분리해 최상위 레벨의 함수로 만들었기 때문에 깊은 중첩이 없습니다. 그리고 콜백 기반 스타일 코드와 동일하게 동작합니다.

그런데 이렇게 작성하면 동작상의 문제는 없지만, 코드가 찢어진 종잇조각 같아 보인다는 문제가 생깁니다. 읽는 것도 어려워집니다.

게다가 `step*`이라고 명명한 함수들은 "멸망의 피라미드"를 피하려는 용도만으로 만들었기 때문에 재사용이 불가능합니다. 그 누구도 연쇄 동작이 이뤄지는 코드 밖에서는 함수들을 재활용하지 않을 것입니다.

멸망의 피라미드를 피할 방법은 몇 가지가 있지만, 가장 좋은 방법 중 하나는 Promise를 사용하는 것입니다.

<br>

## Reference

https://ko.javascript.info/callbacks
