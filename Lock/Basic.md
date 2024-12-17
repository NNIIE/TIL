### 목차
- [Dead Lock](#dead-lock)
- [Mutex](#mutex)
- [Semaphore](#semaphore)
- [Monitor Lock](#monitor-lock)
- [Reentrant Lock](#reentrant-lock)
- [Atomic Object](#atomic-object)
- [CAS](#cas)
- [Lock Striping](#lock-striping)
- [Spin Lock](#spin-lock)

<br>

### Dead Lock
다수의 프로세스나 스레드가 서로가 보유하고 있는 자원을 요구하면서 무한히 대기하는 상태

#### 필요조건
4가지 조건이 모두 성립해야 함

- **상호 배제 (Mutual Exclusion)**
    - 자원은 한번에 하나의 프로세스만 사용할 수 있다.
- **점유 대기 (Hold and Wait)**
    - 프로세스가 이미 하나 이상의 자원을 취득한 상태에서, 다른 프로세스가 사용중인 자원을 추가로 기다린다.
- **비선점 (No Preemption)**
    - 프로세스가 점유한 자원을 강제로 빼앗을 수 없다.
- **순환 대기 (Circular Wait)**
    - 프로세스들이 순환하는 형태로 서로의 자원을 요청하고 기다린다.

#### 해결법

- **예방**
    - 데드락 필요조건 4가지 중 1개가 충족되지 않도록 설계
- **회피**
    - 교착상태가 발생할 가능성이 있는 자원 할당을 하지 않고 안전한 상태에서만 자원 요청을 허용하는 방법
- **복구**
    - 데드락을 허용하고, 데드락이 발생하면 복구

<br>

### Mutex
- 임계구역에 하나의 스레드만이 접근할 수 있는 락
- 스레드가 락을 획득할 때까지 대기하며, 락을 획득한 스레드 만이 공유자원에 접근한다.
- 상호 배제를 보장하고, 락을 획득하지 못한 스레드는 대기상태에 들어간다.
- 락을 소유한 스레드 만이 락을 해제할 수 있다.

<br>

### Semaphore
- 지정된 개수만큼의 스레드가 자원에 접근할 수 있는 락

<br>

### Volatile
- 변수에 붙이면 CPU Cache 대신 Main Memory에 변수를 저장해 읽기와 쓰기연산을 수행
- 하나의 스레드에서만 읽기/쓰기를 하고 다른 스레드들은 읽기작업만 할 경우 사용한다.

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
- synchronized처럼 blocking이 아닌 non-blocking 방식으로 동작
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
   
<br>

### Spin Lock
락을 획득하기 위해 획득할 때 까지 루프를 돌며 락의 상태를 계속 확인하는 방식의 동기화 매커니즘.
- 멀티코어와 락이 짧은 시간동안 점유될 때 유리하다.
- 스레드가 대기상태에 빠지지 않고 CPU를 계속 점유하기 때문에 컨텍스트 스위칭 비용을 아낀다.
- 임계영역의 작업이 복잡하거나 락의 점유-반환 시간이 길다면 CPU가 다른 스레드의 작업을 처리할 기회 비용을 날리며 busy waiting 상태에 빠져 비효율적







 
