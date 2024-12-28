### 목차
- [MVCC](#mvcc)
- [Undo log](#undo-log)
- [Exclusive lock](#exclusive-lock)
- [Shared lock](#shared-lock)
- [Optimistic lock](#optimistic-lock)
- [Pessimistic lock](#pessimistic-lock)
- [Skip Lock](#skip-lock)
- [Gap Lock](#gap-lock)
- [Next Key Lock](#next-key-lock)

<br>

### MVCC
- Multi Version Concurrency Content (다중 버전 동시성 제어) 즉, 하나의 레코드에 여러 버전이 관리 됨
- 트랜잭션에 대해 별도의 스냅샷을 제공함으로 써, 락 없이 읽기, 쓰기 작업이 동시에 가능함.
- 락을 사용하지 않으므로 매우 빠르지만 버전 충돌 시 어플리케이션 레벨에서 해결해야 함.
- MySQL InnoDB는 undo log와 트랜잭션 id를 사용해 관리
    - 데이터를 변경할 때 마다 변경사항을 undo 영역에 저장한다.
    - **Repeatable Read**가 기본 격리 수준이며, 이를 통해 Non-Blocking Read를 구현.

<br>

### Undo log
- 트랜잭션과 격리수준을 보장하기 위해 백업해둔 변경 전의 데이터
    - 트랜잭션 롤백 대비 용도
    - 트랜잭션의 격리 수준을 유지하면서 높은 동시성을 제공하는 데 사용
        - Repeatable Read에서 MVCC를 지키기 위해 언두 영역에 백업 된 이전 데이터를 사용해 동일 트랜잭션 내에서 동일한 결과를 보여줄 수 있도록 보장

<br>

### Exclusive lock
- 베타적 락 (x lock)
- 특정 트랜잭션이 데이터에 대해 쓰기 작업을 수행할 때 획득하는 락
- 트랜잭션이 Exclusive lock을 획득하면, 같은 자원에 대해 다른 트랜잭션이 어떠한 형태의 락도 획득 불가능, 단순 읽기는 가능 (MVCC)
- Exclusive lock이 걸린 데이터에 다른 트랜잭션이 쓰기작업을 하려면 대기상태(락 경쟁)에 들어가야 함.

<br>

### Shared lock
- 공유 락, 읽기 락 (s lock)
- 여러 트랜잭션이 동일한 데이터에 공유 락을 생성할 수 있음 하지만 Exclusive Lock은 락을 해제할때까지 기다려아함 즉, 읽을 순 있지만 쓰기는 불가능

<br>

### Optimistic lock 
- 낙관적 락 cas, 스핀락 연산과 비슷, version 사용
- 대부분의 트랜잭션이 충돌하지 않는다고 가정 함.
- DB Lock을 사용하지 않고 어플리케이션 레벨에서 잡아주는 락, version 관리로 충돌을 예방
- 트랜잭션을 필요로 하지 않음, 충돌이 일어났다면 어플리케이션 레벨에서 해결해야 함
- 읽기가 대부분이고 쓰기가 적을 경우 유리하다.

<br>

### Pessimistic lock 
- 비관적 락 모니터락
- 경합이 생길거라 가정하고 트랜잭션이 시작될 때 Shared Lock 또는 Exclusive Lock을 걸고 시작
- 읽기가 많은 경우 손해가 더 크고 데드락이 발생할 가능성이 있음
- 데이터의 무결성이 중요하고, 충돌이 많이 발생해 잦은 롤백이 일어날거라 예상되는 경우 사용하면 좋다.

<br>

### Skip Lock
- 다른 트랜잭션이 lock을 획득한 레코드를 대기하지않고 건너뛰고 처리하는 방식
- 처리하지않고 스킵하기 때문에 추후 처리 필요

<br>

### Gap Lock
- InnoDB의 레코드 간 빈 공간(레코드가 존재하지 않는 빈 공간)에 대해 설정되는 락.
- 팬텀리드 방지 즉, 같은 트랜잭션에서 동일 조건으로 검색했을 때 그 사이 처음에 없던 레코드가 생기는걸 방지

<br>

### Next Key Lock
- InnoDB에서 사용하는 Row Lock + Gap Lock이 결합된 잠금 기법이다.
- 특정 레코드 그 자체”와 “그 레코드 바로 다음 레코드 사이 갭”을 한 번에 묶어서 락을 거는 방식



