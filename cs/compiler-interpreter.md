# 컴파일러(Compiler)와 인터프리터(Interpreter)

고급 언어로 작성된 프로그램을 실행하는 데에는 두 가지 방법이 있습니다. 가장 일반적인 방법은 프로그램을 컴파일하는 것이고, 다른 하나는 프로그램을 인터프리터에 통과시키는 방법입니다.

## 컴파일러

컴파일러는 프로그래밍 언어(소스 코드)를 다른 언어(목적 코드)로 변환하는 프로그램입니다.

우리가 흔히 컴파일러를 말할 때는 high-level programming language를 low-level programming language (e.g. assembly language, object code, or machine code) 로 변환하는 프로그램을 가리킵니다.

### 컴파일러의 조건

컴파일러가 꼭 지켜야 할 두 가지 조건이 있습니다.

1. 컴파일러는 변환 과정에서 프로그램의 뜻을 보존해야 합니다. 즉, 컴파일된 파일의 실행 결과가 원본 코드에서 의도한 것과 다르지 않아야 합니다.
2. 컴파일러는 입력으로 들어온 프로그램을 어떤 면에서든지 개선해야 합니다. 예를 들어, 소스 코드를 기계어로 옮긴다면 기계가 이해할 수 없었던 언어를 기계가 이해할 수 있게 개선한 것이 됩니다. 같은 언어로 옮기는 경우에는 성능이 개선되는 등의 장점이 있어야 합니다.

### 컴파일러의 실행 단계

1. 구문 분석
    - 소스 코드를 읽어 개별 문법요소 (연산자, 괄호, 식별자 등) 단위로 자른 후, 이 문법요소를 해석하여 추상 구문 트리를 생성합니다. 이 과정에서 문법에 맞지 않는 소스 코드는 사용자에게 알려줍니다.
2. 최적화
    - 추상 구문 트리를 분석하여 최적화를 수행합니다. 도달할 수 없는 코드를 식별하거나, 상수 표현식을 미리 계산해 두거나, 루프 풀기 등 대부분의 최적화가 이 단계에서 수행됩니다.
3. 코드 생성
    - 최적화된 구문 트리로부터 목적 코드를 생성합니다. 목표 언어가 기계어일 경우, 레지스터 할당, 연산 순서 바꾸기 등 하드웨어에 맞는 최적화가 이 단계에서 수행됩니다. 대부분의 하드웨어 최적화 알고리즘은 NP 복잡도를 갖지만, 휴리스틱을 통해 많은 최적화가 수행됩니다.
4. 링킹
    - 목적 코드가 기계어일 경우, 여러 라이브러리 목적 코드를 묶어 하나의 실행 파일을 생성하게 됩니다. 이 과정은 링커에 의해 수행되며, 어떤 사람들은 링커를 컴파일러의 일부로 간주하지 않기도 합니다.

### 컴파일러의 종류

-   목적 코드가 실행되는 플랫폼(타겟 플랫폼, the target platform)
    1. 네이티브 컴파일러(native or hosted compiler)
        - 컴파일러가 실행된 환경 (컴퓨터, OS 등) 과 같은 환경에서 목적 코드가 실행되는 것을 전제로 합니다.
        - e.g. 윈도우에서는 exe파일, 리눅스에서는 리눅스 실행파일이 생성
    2. 크로스 컴파일러(cross compiler)
        - 목적 코드가 다른 환경에서 실행될 수 있도록 제작됩니다. 임베디드 시스템 등 소프트웨어 개발에 충분한 환경을 갖추지 못한 환경에서 동작할 프로그램을 만들기 위해 사용됩니다.
        - e.g. 리눅스(x86)에서 ARM용 임베디드 시스템용 프로그램이나 커널을 개발하기 위해 ARM용 gcc를 사용하면 ARM 코드의 실행파일이 생성됩니다.
    3. VM (Virtual Machine) 에서 실행되는 파일을 만드는 컴파일러
-   컴파일 결과가 machine code가 아닌 경우
    1. Source-to-source compiler
        - 컴파일 원본과 결과가 모두 high-level language입니다.
        - lagnuage translator, language converter, language rewritter (언어의 변화가 없는 경우) 등으로도 불립니다.
        - e.g. OpenMP, Fortran의 `DOALL`
    2. Bytecode compiler
        - 가상 환경의 어셈블리 언어로 컴파일됩니다.
        - e.g. Java, Python
    3. Just-in-time compiler(JIT compiler)
        - 런타임시에 컴파일이 진행됩니다.
        - JIT 컴파일러는 일반적으로 인터프리터 안에서 동작합니다. 인터프리터가 특정 코드가 자주 실행되는 것을 인지하면, JIT 컴파일러를 호출하여 해당 코드를 컴파일합니다. (성능 향상)
        - e.g. Python, JavaScript, Smalltalk, Java, Microsoft .NET's CIL(Common Intermdeiate Language)
        - Java같은 경우에는 먼저 bytecode compiler를 통해 컴파일하고, 머신 안에서 bytecode interpreter가 bytecode를 실행하는 도중에 성능 향상이 필요하면 JIT 컴파일러를 호출하여 bytecode를 machine code로 변환합니다.
    4. Hardware compiler(synthesis tool)
        - 입력 언어가 HDL(Hardware Description Language)이고 결과가 netlist 형식의 하드웨어 설정에 대한 설명입니다.
    5. Assembler
        - 인간이 읽을 수 있는 어셈블리어를 machine code로 변환합니다.
        - 반대 과정 (machine code --> 어셈블리어) 를 수행하는 프로그램을 disassembler라고 합니다.
    6. Decompiler
        - low-level language를 higher language로 변환합니다.
    7. Binary recompiler
        - Object code를 최적화와 변형을 적용한 같은 타입의 object code로 변환합니다.

<br>

## 인터프리터

인터프리터란 프로그래밍 언어의 소스 코드를 바로 실행하는 컴퓨터 프로그램 또는 환경으로서, 다음 중 적어도 한 가지 기능을 가집니다.

1. 소스 코드를 직접 실행합니다. - LISP, BASIC
2. 소스 코드를 효율적인 다른 중간 코드로 변환하고, 변환한 것을 바로 실행합니다. - Perl, Raku, Python, MATLAB, Ruby
3. 인터프리터 시스템의 일부인 컴파일러가 만든 미리 컴파일된 저장 코드의 실행을 호출합니다. - UCSD Pascal

### 인터프리터의 단점

-   컴파일된 프로그램을 실행시키는 것보다 느립니다.

### 인터프리터의 장점

-   기계어 명령어가 만들어지는 컴파일 단계를 거칠 필요가 없습니다. 원시 프로그램의 크기가 크다면, 컴파일 과정은 상당한 시간이 걸릴 수 있습니다. 이와는 달리 인터프리터는 고급 프로그램을 즉시 실행시킬 수 있습니다. 이런 이유 때문에 인터프리터는 프로그램의 개발 단계에서 사용될 수 있는데, 인터프리터를 사용하면 적은 양의 코드를 추가하고 이를 빠르게 테스트할 수 있습니다.
-   인터프리터를 이용하면 프로그래밍을 대화식으로 할 수 있기 때문에, 학생들의 교육용으로 많이 사용됩니다.

<br>

## Reference

https://ko.wikipedia.org/wiki/%EC%BB%B4%ED%8C%8C%EC%9D%BC%EB%9F%AC

https://en.wikipedia.org/wiki/Compiler

https://ko.wikipedia.org/wiki/%EC%9D%B8%ED%84%B0%ED%94%84%EB%A6%AC%ED%84%B0

https://en.wikipedia.org/wiki/Interpreter_(computing)
