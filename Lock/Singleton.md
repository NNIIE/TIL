### Eager Initialization
```java
public class Singleton {

    private static final Singleton instance = new Singleton();
  
    private Singleton() {}
  
    public static Singleton getInstance() {
        return instance;  
    }

}
```
- 장점
  - thread-safe.
- 단점
  - 인스턴스가 사용되지 않아도 클래스 로딩시점에 무조건 인스턴스를 생성한다.
 
<br>

### Lazy Initialization - Synchronized
```java
public class Singleton {
  
    private static Singleton instance;
  
    private Singleton() {}
  
    public static synchronized Singleton getInstance() {
        if (instance == null) {
            instance = new Singleton();
        }
        return instance;
    }

}
```
- 장점
  - synchronized에 의해 thread-safe
- 단점
  - synchronized에 의해 호출때마다 lock이 걸리므로 병목 발생
 
<br>

### Double-Checked Locking
```java
public class Sington {

    private static volatile Singleton instance;

    priavte Singleton() {}

    public static Singleton getInstance() {
        if (instance == null) {
            // 인스턴스가 없을 경우에만 Singleton class에 동기화
            synchronized (Singleton.class) {
                if (inscance == null) {
                    instance = new Singleton();
                }
            }
        }
        return instance;
    }

}
```
- 장점
  - Lazy Initialization, thread-safe
  - 한번 인스턴스가 생성된 뒤부터 락에 걸리지 않음
- 단점
  - jdk 1.5 미만이면 thread-safe 하지 않다.
 
<br>

### Lazy Holder (Bill Pugh Solution)
```java
public class Singleton {

    private Singleton() {}

    // Holder 클래스는 getInstance() 메서드가 호출될때 초기화 되는데, 이 시점에 JVM 차원에서 동기화 되어있다.
    private static class SingletonHolder {
        private static final Singleton INSTANCE = new Singleton();
    }

    public static Singleton getInstance() {
        return SingletonHolder.INSTANCE
    }

}
```
- Lazy Initialization, thread-safe


<br>

### Enum
- thread-safe
- 리플렉션에 의해 singleton이 깨지지 않는다.
  - enum은 리플렉션으로 인스턴스 생성 불가
- 상속 불가

<br>

### DI 컨테이너
- 스프링같은 DI 프레임워크는 Bean Scope로 Singleton을 지원.
- 테스트와 mocking에 용이하다.
- Lazy Initialization 지원, @PostConstruct 등의 다양한 라이프사이클 제공
- 프레임워크에 의존적이다.























