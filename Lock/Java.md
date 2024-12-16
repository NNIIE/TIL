### 목차
- [목차](#목차)
    - [Monitor Lock](#monitor-lock)
    - [Reentrant Lock](#reentrant-lock)
    - [Atomic Object](#atomic-object)
    - [CAS](#cas)
    - [Lock Striping](#lock-striping)

<br>

### Monitor Lock
- 암묵적 락.
- 자바에서 모든 객체는 고유의 Lock을 지니고 있다.
  - synchronized 키워드를 통해 암묵적인 모니터 락(Monitor Lock)을 획득할 수 있다.
 
<br>

### Reentrant Lock
- 명시적 락, 재진입이 가능한 락
- 특정 조건에서 lock을 풀고 나중에 다시 lock을 얻어 이후의 작업을 수행할 수 있다.
  - 이는 데드락을 방지하고, 복잡한 동기화 상황에서 유용하게 사용된다.
- ReentrantLock을 사용하면 락을 획득하고 해제하는 과정을 개발자가 직접 제어할 수 있다.
```java
ReentrantLock lock = new ReentrantLock();
lock.lock();
try {
    // 공유 자원에 대한 접근 코드
} finally {
    // 작업이 완료된 후에는 반드시 finally 블록을 통해 락을 해제
    lock.unlock();
}
```
 
<br>

### Atomic Object
- java.util.concurrent.atomic 패키지에서 제공하는 객체들.
- 내부적으로 volatile 필드를 가진다.
- compareAndSet(): CAS 연산 지원  
```java
public class Counter {
    AtomicInteger atomicInt = new AtomicInteger;

    void incrementMillionTimes() {
        for (int i = 0; i < 1000000; i++) {
            atomicInt.incrementAndGet();
        }
    }
}

@Test
void atomic_increment() {
    incrementInNewThread();
    incrementInNewThread();
    Thread.sleep(500);

    assertThat(counter.atomicInt.get()).isEqualTo(2000000);
}
```

<br>

### CAS
- 이 연산은 비교 후 교체를 원자적으로 수행한다.
  - 하드웨어 레벨 (기계어)로 지원된다.
  - 현재 메모리에 저장된 값과 내가 기대하는 값을 비교
  - 현재값과 기대값이 일치하면, 지정한 새로운 값으로 교체하고 true 반환
  - 현재값과 기대값이 일치하지 않으면, 새로운 값으로 교체하지 않고 false 반환
- 이 일련의 로직이 하나의 단위로 처리된다. (비교와 교체를 수행하는 동안 다른 스레드가 이 변수에 개입해서 중간 상태를 만들수 없음)
- 자바는 CPU의 CAS 명령어를 호출할 수 없어서 Unsafe 클래스를 통해 원자적 연산을 수행한다.

<br>

### Lock Striping
- lock이 여러 버킷 또는 스트라이프에서 발생한다. 즉, 버킷에 엑세스 하면 해당 버킷만 잠긴다.
- Lock Striping 은 스레드 경합을 줄이고 락의 사용 빈도를 줄인다.
  - ex) ConcurrentHashMap
    - 자바 8 이전: Segment 배열과 ReentrantLock 을 사용한 락 스트라이핑 기법을 사용
    - 자바 8 이후: Node 배열과 CAS(Compare And Swap) 연산을 사용한 구현체로 변경
    - 기본적으로 16개의 버킷을 가지므로 총 16개의 lock을 가진다.
  






 
