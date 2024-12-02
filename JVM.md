### JVM(Java Virtual Machine) 의 구조와 동작방식
###### JVM은 자바 언어에서만 사용되는 것이 아니라 코틀린, 스칼라 언어에서도 JVM 동작 방식을 그대로 따른다.
###### 아래의 그림은 자바 애플리케이션의 구동원리인데 JVM은 빨간 박스를 친부분만 컴파일된 .class 파일을 어떠한 처리를 거쳐 프로그램을 실행하는 과정이다.
![image](https://github.com/user-attachments/assets/05017615-cb90-45f8-8fdb-89073506f4e6)

### JVM 동작 방식
###### 자바 가상머신(Java Virtual Machine)인 JVM의 역할은 자바 애플리케이션을 클래스 로더를 통해 읽어 자바 API와 함꼐 실행하는 것이다.
![image](https://github.com/user-attachments/assets/7e676841-8f1b-4373-b8c9-b74a39031899)
###### 1. 자바 프로그램을 실행하면 JVM은 OS로부터 메모리를 할당받는다.
###### 2. 자바 컴파일러(javac)가 자바 소스코드(.java)를 자바 바이트 코드(.class)로 컴파일 한다.
###### 3. Class Loader는 동적 로딩을 통해 필요한 클래스들을 로딩 및 링크 하여 Runtime Data Area(실질적 메모리를 할당 받아 관리하는 영역)에 올린다.
###### 4. Runtime Data Area에 로딩 된 바이트 코드는 Execution Engine을 통해 해석된다.
###### 5. Execution Engine에 의해 Garbage Collector의 작동과 Thread 동기화가 이루어진다. 

### JVM의 구조
###### 아래는 Class Loader <-> Execution Engine <-> Runtime Data Area부분을 좀 더 상세화한 도식도이다.
![image](https://github.com/user-attachments/assets/0cd67b8d-1e0a-482f-a04c-e6f3c9eac26d)
###### JVM의 구성요소는 아래와 같다. 
    - 클래스 로더(Class Loader)
    - 실행엔진(Execution Engine)
        - 인터프리터(Interpreter)
        - JIT 컴파일러(Just-in-Time)
        - 가비지 콜렉터(Garbage collector)
    - 런타임 데이터 영역 (Runtime Data Area
        - 메소드 영역
        - 힙 영역
        - PC Register
        - 스택 영역
        - 네이티브 메소드
        - JNI: 네이티브 메소드 인터페이스 (Native Medthod Interface)
        - 네이티브 메소드 라이브러리 (Native Method Library)

### 클래스 로더(Class Loader)
###### 클래스 로더는 JVM 내로 클래스 파일(*.class)을 동적으로 로드하고 링크를 통해 배치하는 작업을 수행하는 모듈이다.
###### 즉 로드된 바이트 코드들을 엮어서 JVM의 메모리 영역인 Runtime Data Areas에 배치한다.
###### 클래스를 메모리에 올리는 로딩 기능은 한번에 메모리를 올리지 않고 어플리케이션에서 필요한 경우 동적으로 메모리에 적재하게 된다. 
###### 클래스 파일의 로딩순서는 다음과 같이 3단계로 구성된다.(Loading -> Linking -> Initialization)
![image](https://github.com/user-attachments/assets/ce9476a9-cb59-4495-8b77-82699555772d)

###### 1. Loading(로드): 클래스 파일을 가져와 JVM의 메모리에 로드한다.
###### 2. Linking(링크): 클래스 파일을 사용하기 위해 검증하는 과정이다. 
###### - Verifiying(검증): 읽어드린 클래스가 JVM 명세에 명시된 대로 구성되어 있는지 검사한다.
###### - Preparing(준비): 클래스가 필요로 하는 메모리를 할당한다.
###### - Resolving(분석): 클래스의 상수 풀 내 모든 심볼릭 레퍼런스를 다이렉트 레퍼런스로 변경한다.
###### 3. Initializaionn(초기화): 클래스 변수들을 적절한 값으로 초기화한다.(static 필드들은 설정된 값으로 초기화)

### 실행 엔진(Execution Engine)
###### 실행 엔진은 클래스 로더를 통해 런타임 데이터 영역에 배치된 바이트 코드를 명령어 단위로 읽어서 실행한다.
###### 자바 바이트 코드(*.class)는 기계가 바로 수행할 수 있는 언어보다는 가상머신이 이해할 수 있는 중간 레벨로 컴파일 된 코드이다. 그래서 실행 엔진은 이와 같은 바이트 코드를 실제 JVM 내부에서 기계가 실행할 수 있는 형태로 변경해준다. 
###### 이러한 수행 과정에서 실행 엔진은 인터프리터와 JIT 컴파일러 두 가지 방식을 혼합하여 바이트 코드를 실행한다. 

### 인터프리터(Interpreter)
###### 바이트 코드 명령어를 하나씩 읽어 해석하고 바로 실행한다.
###### JVM안에서 바이트코드는 기본적으로 인터프리터 방식으로 동작한다. 다만 같은 메소드라도 여러번 호출이 된다면 매번 해석하고 수행해야되서 전체적인 속도는 느리다.

### JIT 컴파일러(Just-In-Time Compiler)
###### Interpreter의 단점을 보완하기 위해 도입된 방식으로 반복되는 코드를 발견하여 바이트 코드 전체를 컴파일하여 Native Code로 변경하고 이후에는 해당 메서드를 더 이상 인터프리팅 하지 않고 캐싱해 두었다가 네이티브 코드로 직접 실행하는 방식이다.
###### 전체적인 속도는 인터프리팅 방식보다 빠르나 바이트코드를 Native Code로 변환하는 데 비용이 소요되므로 JVM은 모든 코드를 JIT 컴파일러 방식으로 실행하지 않고 인터프리터 방식을 사용하다 일정 기준을 넘어가면 JIT 컴파일 방식으로 명령어를 실행하는 식으로 진행한다.

### 가비지 컬렉터(GC)
###### 자바 가상 머신은 가비지 컬렉터(GC)를 이용하여 Heap 메모리 영역에서 더는 사용하지 않는 메모리를 자동으로 회수해 준다.
###### 자바는 이 GC를 통해 자동으로 메모리를 실시간 최적화 시켜준다. 따라서 개발자가 따로 메모리를 관리하지 않아도 되므로 손쉽게 프로그래밍을 할 수 있도록 도와준다.
###### Full GC가 발생하는 경우 GC를 제외한 모든 스레드가 중지되므로 장애가 발생할 수 있다.

### 런타임 데이터 영역(Runtime Data Area)
![image](https://github.com/user-attachments/assets/7cd7edb2-7d31-44c8-9d40-63e5376af7ed)
###### 런타임 데이터 영역은 JVM의 메모리 영역으로 자바 애플리케이션을 실행할 때 사용되는 데이터들을 적재하는 영역이다.
###### 런타임 데이터 영역은 Method Area, Heap Area, Stack Area, PC Register, Native Method Stack로 나눌 수 있다.
###### Method Area, Heap Area 는 모든 쓰레드(Thread)가 공유하는 영역이고 나머지 나머지 Stack Area, PC Register, Native Method Stack 은 각 쓰레드 마다 생성되는 개별 영역이다.
![image](https://github.com/user-attachments/assets/5fed2d22-a608-446c-98d0-45e63947c891)

### 메서드 영역(Method Area)
###### 메서드 영역은 JVM이 시작될 떄 생성되는 공간으로 바이트 코드(.class)를 처음 메모리 공간에 올릴 때 초기화되는 대상을 저장하기 위한 메모리 공간이다.
###### JVM이 동작하고 클래스가 로드될 때 적재되서 프로그램이 종료될 때까지 저장된다.



