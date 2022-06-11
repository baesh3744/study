# 실행 컨텍스트(Execution Context)

## 실행 컨텍스트란

ECMAScript spec에서는 실행 컨텍스트를 실행 가능한 코드를 형상화하고 구분하는 추상적인 개념이라고 정의합니다. 즉, 실행 컨텍스트는 실행 가능한 코드가 실행되기 위해 필요한 환경이라고 할 수 있습니다. 여기서 말하는 실행 가능한 코드는 아래와 같습니다.

-   전역 코드 - 전역 영역에 존재하는 코드
-   Eval 코드 - Eval 함수로 실행되는 코드
-   함수 코드 - 함수 내에 존재하는 코드

일반적으로 실행 가능한 코드는 전역 코드와 함수 코드입니다.

JavaScript 엔진은 코드를 실행하기 위하여 실행에 필요한 여러가지 정보를 알고 있어야 합니다. 실행에 필요한 여러가지 정보란 아래와 같은 것들이 있습니다.

-   변수 - 전역변수, 지역변수, 매개변수, 객체의 프로퍼티
-   함수 선언
-   변수의 유효범위(scope)
-   this

이와 같이 실행에 필요한 정보를 형상화하고 구분하기 위해 JavaScript 엔진은 실행 컨텍스트를 물리적 객체의 형태로 관리합니다.

```javascript
var x = "xxx";

function foo() {
    var y = "yyy";

    function bar() {
        var z = "zzz";
        console.log(x + y + z);
    }
    bar();
}
foo();
```

위 코드를 실행하면, 아래와 같이 실행 컨텍스트 스택이 생성하고 소멸합니다. 현재 실행 중인 컨텍스트에서 이 컨텍스트와 관련없는 코드 (예를 들어 다른 함수) 가 실행되면 새로운 컨텍스트가 생성됩니다. 이 컨텍스트는 스택에 쌓이게 되고 컨트롤(제어권)이 이동합니다.

<p align="center">
    <img width="600" src="https://user-images.githubusercontent.com/22341362/146874885-214bddaf-60ab-4ac0-8401-2597e3d61046.png" alt="Execution context stack">
</p>

1. 컨트롤이 실행 가능한 코드로 이동하면 논리적 스택 구조를 가지는 새로운 실행 컨텍스트 스택이 생성됩니다. 스택은 LIFO의 구조를 가지는 나열 구조입니다.
2. 전역 코드로 컨트롤이 진입하면 전역 실행 컨텍스트가 생성되고 실행 컨텍스트 스택에 쌓입니다. 전역 실행 컨텍스트는 애플리케이션이 종료될 때 (웹 페이지를 나가거나 브라우저를 닫을 때) 까지 유지됩니다.
3. 함수를 호출하면 해당 함수의 실행 컨텍스트가 생성되며 직전에 실행된 코드 블록의 실행 컨텍스트 위에 쌓입니다.
4. 함수 실행이 끝나면 해당 함수의 실행 컨텍스트를 파기하고 직전의 실행 컨텍스트에 컨트롤을 반환합니다.

<br>

## 실행 컨텍스트의 3가지 객체

실행 컨텍스트는 실행 가능한 코드를 형상화하고 구분하는 추상적인 개념이지만 물리적으로는 객체의 형태를 가지며 아래의 3가지 프로퍼티를 소유합니다.

### 1. Variable Object(VO, 변수객체)

실행 컨텍스트가 생성되면 JavaScript 엔진은 실행에 필요한 여러 정보들을 담을 객체를 생성합니다. 이를 Variable Object라고 합니다. Variable Object는 코드가 실행될 때 엔진에 의해 참조되며 코드에서는 접근할 수 없습니다.

Variable Ojbect는 아래의 정보를 담는 객체입니다.

-   변수
-   매개변수(parameter)와 인수 정보(arguments)
-   함수 선언 (함수 표현식은 제외)

Variable Object는 실행 컨텍스트의 프로퍼티이기 때문에 값을 갖는데 이 값은 다른 객체를 가리킵니다. 그런데 전역 코드 실행시 생성되는 전역 컨텍스트의 경우와 함수를 실행할 때 생성되는 함수 컨텍스트의 경우, 가리키는 객체가 다릅니다. 이는 전역 코드와 함수의 내용이 다르기 때문입니다. 예를 들어 전역 코드에는 매개변수가 없지만 함수에는 매개변수가 있습니다.

Variable Object가 가리키는 객체는 아래와 같습니다.

-   전역 컨텍스트의 경우, Variable Object는 유일하며 최상위에 위치하고 모든 전역 변수, 전역 함수 등을 포함하는 전역 객체(GO, Global Object)를 가리킵니다. 전역 객체는 전역에 선언된 전역 변수와 전역 함수를 프로퍼티로 소유합니다.

<p align="center">
    <img height="200" src="https://user-images.githubusercontent.com/22341362/146875964-febe88b6-912e-4311-97b4-2d8f3fcfd8fe.png" alt="Global Object">
</p>

-   함수 컨텍스트의 경우, Variable Object는 활성 객체(AO, Activation Object)를 가리키며 매개변수와 인수들의 정보를 배열의 형태로 담고 있는 객체인 arguments object가 추가됩니다.

<p align="center">
    <img height="200" src="https://user-images.githubusercontent.com/22341362/146876177-c3b1f45b-30cb-4be6-8249-964438f36483.png" alt="Activation Object">
</p>

### 2. Scope Chain(SC)

Scope Chain은 일종의 리스트로서 전역 객체와 중첩된 함수의 스코프의 레퍼런스를 차례로 저장하고 있습니다. 다시 말해, Scope Chain은 해당 전역 또는 함수가 참조할 수 있는 변수, 함수 선언 등의 정보를 담고 있는 전역 객체 또는 활성 객체의 리스트를 가리킵니다.

현재 실행 컨텍스트의 활성 객체를 선두로 하여 순차적으로 상위 컨텍스트의 활성 객체를 가리키며 마지막 리스트는 전역 객체를 가리킵니다.

<p align="center">
    <img width="450" src="https://user-images.githubusercontent.com/22341362/146876511-63c5fb1d-6f6c-4fd0-90eb-9c6ebf206e09.png" alt="Scope Chain">
</p>

> Scope Chain은 식별자 중에서 객체 (전역 객체 제외) 의 프로퍼티가 아닌 식별자 즉, 변수를 검색하는 메커니즘입니다. 식별자 중에서 변수가 아닌 객체의 프로퍼티 (물론 메소드도 포함됩니다) 를 검색하는 메커니즘은 프로토타입 체인(prototype chain)입니다.

엔진은 Scope Chain을 통해 렉시컬 스코프를 파악합니다. 함수가 중첩 상태일 때, 하위함수 내에서 상위함수의 스코프와 전역 스코프까지 참조할 수 있는데 이것은 Scope Chain을 통해 검색이 가능합니다. 함수가 중첩되어 있으면 중첩될 때마다 부모 함수의 스코프가 자식 함수의 Scope Chain에 포함됩니다. 함수 실행 중에 변수를 만나면 그 변수를 우선 현재 스코프 즉, 활성 객체에서 검색해보고, 만약 검색에 실패하면 Scope Chain에 담겨진 순서대로 그 검색을 이어가게 됩니다.

Scope Chain은 함수의 히든 프로퍼티인 `[[Scope]]`로 참조할 수 있습니다.

### 3. this Value

this 프로퍼티에는 this 값이 할당됩니다. this에 할당되는 값은 함수 호출 패턴에 의해 결정됩니다.

<br>

## 실행 컨텍스트의 생성 과정

```javascript
var x = "xxx";

function foo() {
    var y = "yyy";

    function bar() {
        var z = "zzz";
        console.log(x + y + z);
    }
    bar();
}
foo();
```

### 1. 전역 코드에의 진입

컨트롤이 실행 컨텍스트에 진입하기 이전에 유일한 전역 객체가 생성됩니다. 전역 객체는 단일 사본으로 존재하며 이 객체의 프로퍼티는 코드의 어떠한 곳에서도 접근할 수 있습니다. 초기 상태의 전역 객체에는 빌트인 객체 (Math, String, Array 등) 과 BOM, DOM이 설정되어 있습니다.

<p align="center">
    <img width="400" src="https://user-images.githubusercontent.com/22341362/146877953-62fda153-e78b-43df-ba93-8aefa83edf04.png" alt="Initial execution context">
</p>

전역 객체가 생성된 이후, 전역 코드로 컨트롤이 진입하면 전역 실행 컨텍스트가 생성되고 실행 컨텍스트 스택에 쌓입니다.

<p align="center">
    <img width="400" src="https://user-images.githubusercontent.com/22341362/146878135-6ccc3a95-8851-4bda-b54a-d7f8a06dd743.png" alt="Create global execution context">
</p>

그리고 이 실행 컨텍스트를 바탕으로 아래의 과정이 실행됩니다.

1. Scope Chain의 생성과 초기화
2. 변수 객체화 실행
3. this value 결정

### 1.1. Scope Chain의 생성과 초기화

실행 컨텍스트가 생성된 이후 가장 먼저 Scope Chain의 생성과 초기화가 실행됩니다. 이때 Scope Chain은 전역 객체의 레퍼런스를 포함하는 리스트가 됩니다.

<p align="center">
    <img width="400" src="https://user-images.githubusercontent.com/22341362/146878350-bf2b85ed-eb14-4185-98d4-414353c8714b.png" alt="Create and initialize Scope Chain">
</p>

### 1.2. Variable instantiation(변수 객체화) 실행

Scope Chain의 생성과 초기화가 종료되면 variable instantiation가 실행됩니다. Variable instantiation은 Variable Object에 프로퍼티와 값을 추가하는 것을 의미합니다. 변수 객체화라고 변역하는 이유는 변수, 매개변수와 인수 정보, 함수 선언을 Variable Object에 추가하여 객체화하기 때문입니다.

전역 코드의 경우, Variable Object는 Global Object를 가리킵니다.

<p align="center">
    <img width="400" src="https://user-images.githubusercontent.com/22341362/146878802-f41bf759-c73b-43eb-bd06-4919eea35fdf.png" alt="Variable instantiation">
</p>

Variable instantiation은 아래의 순서로 Variable Object에 프로퍼티와 값을 설정합니다.

1. (함수 코드인 경우) 매개변수(parameter)가 Variable Object의 프로퍼티로, 인수(arguments)가 값으로 설정됩니다.
2. 대상 코드 내의 함수 선언 (함수 표현식 제외) 를 대상으로 함수명이 Variable Object의 프로퍼티로, 생성된 함수 객체가 값으로 설정됩니다. (함수 호이스팅)
3. 대상 코드 내의 변수 선언을 대상으로 변수명이 Variable Object의 프로퍼티로, `undefined`가 값으로 설정됩니다. (변수 호이스팅)

위 예제 코드를 보면 전역 코드에 변수 x와 함수 foo가 선언되었습니다. Variable instantiation의 실행 순서상 우선 (2) 함수 foo의 선언이 처리되고, (3) 그 후 변수 x의 선언이 처리됩니다. 함수 코드가 아닌 전역 코드이기 때문에 (1) 매개변수 처리는 실행되지 않습니다.

### 1.2.1. 함수 foo의 선언 처리

함수 선언은 variable instantiation 실생 순서 2에 적힌 것처럼 함수명 foo가 Variable Object (전역 코드인 경우 Global Object) 의 프로퍼티로, 생성된 함수 객체가 값으로 설정됩니다.

<p align="center">
    <img width="550" src="https://user-images.githubusercontent.com/22341362/146879542-f251d213-2615-41f8-b9ab-8dea3790d971.png" alt="Function foo declaration">
</p>

생성된 함수 객체는 `[[Scopes]]` 프로퍼티를 가지게 됩니다. `[[Scopes]]` 프로퍼티는 함수 객체만이 소유하는 내부 프로퍼티(internal property)로서 함수 객체가 실행되는 환경을 가리킵니다. 따라서 현재 실행 컨텍스트의 Scope Chain이 참조하고 있는 객체를 값으로 설정합니다. 내부 함수의 `[[Scopes]]` 프로퍼티는 자신의 실행 환경(lexical environment)와 자신을 포함하는 외부 함수의 실행 환경과 전역 객체를 가리키는데 이때 자신을 포함하는 외부 함수의 실행 컨텍스트가 소멸하여도 `[[Scopes]]` 프로퍼티가 가리키는 외부 함수의 실행 환경(activation object)는 소멸하지 않고 참조할 수 있습니다. 이것을 클로저라고 합니다.

지금까지 살펴본 실행 컨텍스트는 아직 코드가 실행되기 이전입니다. 하지만 Scope Chain이 가리키는 Variable Object에 이미 함수가 등록되어 있으므로 이후 코드를 실행할 때 함수 선언식 이전에 함수를 호출할 수 있게 되었습니다.

이때 알 수 있는 것은 함수 선언식의 경우, Variable Object에 함수 표현식과 동일하게 함수명을 프로퍼티로 함수 객체를 할당한다는 것입니다. 단, 함수 선언식은 Variable Object에 함수명을 프로퍼티로 추가하고 함수 객체를 즉시 할당하지만, 함수 표현식은 일반 변수의 방식을 따릅니다. 따라서 함수 선언식의 경우, 선언문 이전에 함수를 호출할 수 있습니다. 이것을 함수 호이스팅(function hoisting)이라고 합니다.

### 1.2.2. 변수 x의 선언 처리

변수 선언은 variable instantiation 실행 순서 3에 적힌 것처럼 변수명 x가 Variable Object의 프로퍼티로, `undefined`가 값으로 설정됩니다. 이것을 좀 더 세분화해보면 아래와 같습니다.

1. 선언 단계(declaration phase) - Variable Object에 변수를 등록합니다. 이 Variable Object는 스코프가 참조할 수 있는 대상이 됩니다.
2. 초기화 단계(initialization phase) - Variable Object에 등록된 변수를 메모리에 할당합니다. 이 단계에서 변수는 `undefined`로 초기화됩니다.
3. 할당 단계(assignment phase) - `undefined`로 초기화된 변수에 실제값을 할당합니다.

`var` 키워드로 선언된 변수는 선언 단계와 초기화 단계가 한번에 이루어집니다. 다시 말해, Scope Chain이 가리키는 Variable Object에 변수가 등록되고 변수는 `undefined`로 초기화됩니다. 따라서 변수 선언문 이전에 변수에 접근하여도 Variable Object에 변수가 존재하기 때문에 에러가 발생하지 않고 `undefined`를 반환합니다. 이것을 변수 호이스팅(variable hoisting)이라고 합니다.

아직 변수 x는 'xxx'로 초기화되지 않았습니다. 이후 변수 할당문에 도달하면 비로소 값의 할당이 이루어집니다.

<p align="center">
    <img width="550" src="https://user-images.githubusercontent.com/22341362/146881215-20761409-bf00-426a-ac97-c757563ffe81.png" alt="Variable x declaration">
</p>

### 1.3. this value 결정

변수 선언 처리가 끝나면 다음은 this value가 결정됩니다. this value가 결정되기 이전에 this는 전역 객체를 가리키고 있다가 함수 호출 패턴에 의해 this에 할당되는 값이 결정됩니다. 전역 코드의 경우, this는 전역 객체를 가리킵니다.

<p align="center">
    <img width="550" src="https://user-images.githubusercontent.com/22341362/146881365-8a40b62f-1a8b-494a-9fde-4748e2545bff.png" alt="Variable x declaration">
</p>

전역 컨텍스트(전역 코드)의 경우, Variable Object, Scope Chain, this 값은 언제나 전역 객체입니다.

### 2. 전역 코드의 실행

지금까지는 코드 실행 환경을 갖추기 위한 사전 준비였습니다. 코드의 실행은 지금부터 시작됩니다.

위 예제를 보면 전역 변수 x에 문자열 'xxx' 할당과 함수 foo의 호출이 실행됩니다.

### 2.1. 변수 값의 할당

전역 변수 x에 문자열 'xxx'를 할당할 때, 현재 실행 컨텍스트의 Scope Chain이 참조하고 있는 Variable Object를 선두부터 검색하여 변수명에 해당하는 프로퍼티가 발견되면 값을 할당합니다.

<p align="center">
    <img width="550" src="https://user-images.githubusercontent.com/22341362/146881691-f54268ab-1d1e-4fed-8978-b24722cdf1ea.png" alt="Assign value to variable x">
</p>

### 2.2. 함수 foo의 실행

전역 코드의 함수 foo가 실행되기 시작하면 새로운 함수 실행 컨텍스트가 생성됩니다. 함수 foo의 실행 컨텍스트로 컨트롤이 이동하면 전역 코드의 경우와 마찬가지로 1. Scope Chain의 생성과 초기화, 2. Variable instantiation 실행, 3. this value 결정이 순차적으로 실행됩니다.

단, 전역 코드와 다른 점은 이번에 실행되는 코드는 함수 코드라는 것입니다. 따라서 1 ~ 3의 과정은 전역 코드의 룰이 아닌 함수 코드의 룰이 적용됩니다.

<p align="center">
    <img width="550" src="https://user-images.githubusercontent.com/22341362/146883114-804e585b-ef2f-4fd2-94a2-36237dc2000c.png" alt="Create execution context of function foo">
</p>

### 2.2.1. Scope Chain의 생성과 초기화

함수 코드의 Scope Chain의 생성과 초기화는 우선 Acitvaion Object에 대한 레퍼런스를 Scope Chain의 선두에 설정하는 것으로 시작됩니다.

Activation Object는 우선 arguments 프로퍼티의 초기화를 실행하고 그 후 variable instantiation이 실행됩니다. Activation Object는 스펙 상의 개념으로 프로그램이 Activation Object에 직접 접근할 수 없습니다. 그러나 Activation Object의 프로퍼티로의 접근은 가능합니다.

<p align="center">
    <img width="550" src="https://user-images.githubusercontent.com/22341362/146883584-61987875-1ada-45b9-a91b-0841866b10e7.png" alt="Create and bind Activation Object">
</p>

그 후, caller (전역 컨텍스트) 의 Scope Chain이 참조하고 있는 객체가 Scope Chain에 push됩니다. 이 경우, 함수 foo를 실행한 직후 실행 컨텍스트의 Scope Chain은 Activation Object (함수 foo의 실행으로 만들어진 AO-1) 과 전역 객체를 순차적으로 참조하게 됩니다.

<p align="center">
    <img width="550" src="https://user-images.githubusercontent.com/22341362/146883892-9ae02d93-92c6-4c1c-acac-10dbdbf48a7c.png" alt="Push caller's [[Scope]] to Scope Chain">
</p>

### 2.2.2. Variable instantiation 실행

함수 코드의 경우, Scope Chain의 생성과 초기화에서 생성된 Activation Object를 Variable Object로서 variable instantiation이 실행됩니다. 이것을 제외하면 전역 코드의 경우와 같은 처리가 실행됩니다. 즉, 함수 객체를 Variable Object(AO-1)에 바인딩합니다. (프로퍼티는 bar, 값은 새로 생성된 함수 객체, bar 함수 객체의 `[[Scope]]` 프로퍼티 값은 AO-1과 Global Object를 참조하는 리스트)

<p align="center">
    <img width="550" src="https://user-images.githubusercontent.com/22341362/146885017-62579a60-b165-4ec4-bf63-069f81892fbe.png" alt="Function bar declaration">
</p>

변수 y를 Variable Object(AO-1)에 설정합니다. 이때, 프로퍼티는 y, 값은 `undefined`입니다.

<p align="center">
    <img width="550" src="https://user-images.githubusercontent.com/22341362/146885172-6d7e206d-d587-4f3a-a491-ea85f34dd93f.png" alt="Variable y declaration">
</p>

### 2.2.3. this value 결정

내부 함수의 경우, this value는 Global Object입니다.

<p align="center">
    <img width="550" src="https://user-images.githubusercontent.com/22341362/146885291-7562d664-89a0-43ae-b1d4-ae5c4c74fb67.png" alt="Set this value">
</p>

### 3. foo 함수 코드의 실행

이제 함수 foo의 블록 내 코드가 실행됩니다. 위 예제를 보면 변수 y에 문자열 'yyy'의 할당과 함수 bar가 실행됩니다.

### 3.1. 변수 값의 할당

지역 변수 y에 문자열 'yyy'를 할당할 때, 현재 실행 컨텍스트의 Scope Chain이 참조하고 있는 Variable Object를 선두부터 검색하여 변수명에 해당하는 프로퍼티가 발견되면 값 'yyy'를 할당합니다.

<p align="center">
    <img width="550" src="https://user-images.githubusercontent.com/22341362/146885565-30780d22-839b-4cb0-9370-7556fb5e211a.png" alt="Assign value to variable y">
</p>

### 3.2. 함수 bar의 실행

함수 bar가 실행되기 시작하면 새로운 실행 컨텍스트가 생성됩니다.

<p align="center">
    <img width="550" src="https://user-images.githubusercontent.com/22341362/146885709-8d299676-87b2-4069-b316-2a342d1081c6.png" alt="Execute function bar">
</p>

이전 함수 foo의 실행 과정과 동일하게 1 ~ 3이 순차적으로 실행됩니다.

<p align="center">
    <img width="550" src="https://user-images.githubusercontent.com/22341362/146885829-1fb62ddd-cf3d-4154-88bc-213bfeac5161.png" alt="Completed execution context">
</p>

이 단계에서 `console.log(x + y + z);` 구문의 실행 결과는 "xxxyyyzzz"가 됩니다.

-   x: AO-2에서 x 검색 실패 --> AO-1에서 x 검색 실패 --> GO에서 x 검색 성공 (값은 "xxx")
-   y: AO-2에서 y 검색 실패 --> AO-1에서 y 검색 성공 (값은 "yyy")
-   z: AO-2에서 z 검색 성공 (값은 "zzz")

<br>

## Reference

-   [PoiemaWeb - "5.18 실행 컨텍스트와 자바스크립트의 동작 원리"](https://poiemaweb.com/js-execution-context)
