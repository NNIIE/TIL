# 목차


## 자바의 장점
- 객체지향과 SOLID원칙을 잘 지원하는 구조이다.
- **JVM**
    - 플랫폼에 독립적
    - JIT Compiler를 통한 최적화
- **GC**
    - 자동적 메모리 관리
    - 다양한 GC 알고리즘
 
<br>
 
## 컴파일 및 실행되는 과정
1. 작성한 파일이 .class 형태의 바이트 코드로 변환
2. class loader가 클래스파일을 읽을 수 있도록 로드
    1. 상위 계층에서 하위 계층으로 찾아가는 방식
3. jvm이 바이트코드를 인터프리터 형식으로 읽어가며 실행
    1. 자주 사용되는 코드들은 JIT Compiler를 통해 런타임에 바이너리 코드로 미리 만들어 최적화
  
<br>

## Class Loader
런타임 중 클래스파일(바이트코드)을 jvm이 읽을 수 있도록 meta space(메서드영역)에 로드해 주는 역할
### 계층
- Bootstrap ClassLoader
    - JDK 핵심 클래스들을 로드
- Extension ClassLoader (확장 클래스 로드)
    - 확장 자바클래스들을 로드
- Application ClassLoader
    - classpath 즉, 개발자가 작성한 애플리케이션을 로드
### 동작
- 상위계층에서 하위계층으로 찾아가는 형식으로 로드한다.
- Bootstrap에서 찾고 없으면 Extension에서 찾고 없으면 Application 에서 찾는 형태

<br>

## Jit Compiler
- 원래 자바는 인터프리터 형식이었는데 성능이슈때문에 핫스팟(자주 쓰이는 코드, 반복문, 함수 등)을 식별해 런타임에 기계어로 컴파일해 성능을 최적화 하는 컴파일러
- Jit Compiler의 매커니즘 때문에 자바 어플리케이션의 성능이 제대로 나오려면 최초 구동 후 warm-up 시간이 필요하다.

<br>

## PermGen 영역 / meta space 영역
- 자바 8 이후 meta space 영역으로 대체 되었다.
- 클래스/메서드 메타정보, 상수 풀, 정적 변수 등을 저장한다.
- JDK 8 이전 PermGen 영역 이었을 때는 크기가 고정되어 있어 Full GC, oom등에 취약했고, JDK 8 이후 meta space 영역으로 대체 되면서 힙영역안에 속하게 되고 동적으로 메모리 크기가 변할 수 있게 되었다.
- JDK 8 이전에는 어플리케이션을 오래 구동할 경우 PermGen 영역이 꽉 차는 문제가 있었다.

<br>

## Constant Pool
- 리터럴로 선언한 스트링이 위치하는곳.
- JDK 8 이후 Heap 영역에 위치한다.
- JDK 8 이전 버전에선 PermGen에 위치한다.

<br>


<br>


<br>


<br>


<br>


<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>










