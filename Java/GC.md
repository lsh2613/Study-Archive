## 1. Garbage Collection, Garbage Collector
- `가비지(Garbage)`는 유효하지 않은 `메모리`
- `가비지 컬렉션`은 가비지를 회수하는 `과정`을 말한다
- `가비지 컬렉터`는 가비지 컬렉션을 수행하는 `알고리즘` 또는 `도구`를 말한다
- 통상 `GC` = `Garbage Collection` = `Garbage Collector` 같은 의미로 쓰이는 것 같다
- `JVM`은 가비지 컬렉터를 통해 가바지를 `자동`으로 정리해준다

### 1.1 System.gc()
C언어에 경우 free()라는 메소드를 통해 메모리를 반납한다.

Java에서는 System.gc()라는 함수를 통해 `가비지 컬렉션` 호출을 요청할 수 있다.
말 그대로 가바지 컬렉션 호출을 강제하는 것이 아닌 요청이다. 즉, 요청한다해서 무조건 실행된다는 의미가 아니다.

하지만 이 작업은 많은 리소스를 소모하기 때문에 무조건 실행된다는 보장이 없기 때문에 무분별한 사용은 `지양`하는 것이 좋다

## 2. GC의 구조

- 크게 Young Generation(Minor GC), Old Generation(Major GC)으로 나뉜다
- 객체는 `대부분 일회성`이며, 메모리에 오랫동안 남아있는 경우는 드물기 때문에 이 같은 두 영역으로 구분이 된다

![img.png](img.png)

<br>

### 2.1. Young Generation (Minor GC)
- `새롭게 생성된 객체`가 할당되는 영역
- 부분의 객체가 금방 Unreachable 상태가 되기 때문에, 많은 객체가 Young 영역에 생성되었다가 사라진다.

### 2.2. Old Generation (Major GC)
- Young 영역에서 Reachable 상태를 유지하여 살아남은 객체가 복사되는 영역
- 보통 Young 영역의 객체들은 짧은 수명으로 큰 공간을 필요로 하지 않기 때문에, `큰 객체`들은 바로 Old 영역에 할당된다 
- 따라서 Young 영역보다 `크게` 할당되며, 영역의 크기가 큰 만큼 가비지는 `적게` 발생한다
- `Full GC` 라고도 한다

### 2.3. Card Table
예외적인 상황으로 Old 영역의 객체가 Young 영역의 객체를 참조하는 경우를 대비해 Old 영역에 512Byte의 덩어리(Chunk)로 되어 있는 카드 테이블이 존재한다

![img_1.png](img_1.png)

## 3. Garbage Collection(가비지 컬렉션)의 동작 방식
Young 영역과 Old 영역은 서로 다른 메모리 구조로 되어 있기 때문에, 세부적인 동작 방식은 다르다. 
하지만 기본적으로 가비지 컬렉션이 실행된다고 하면 다음의 2가지 공통적인 단계를 따르게 된다.

### 1단계) Stop The World
- 가비지 컬렉션을 실행하기 위해 JVM이 애플리케이션의 `실행을 멈추는 작업`이다. 
- GC가 실행될 때는 GC를 실행하는 쓰레드를 제외한 모든 쓰레드들의 `작업이 중단`되고, GC가 완료되면 작업이 재개된다. 
- 당연히 모든 쓰레드들의 작업이 중단되면 애플리케이션이 멈추기 때문에, GC의 성능 개선을 위해 튜닝을 한다고 하면 보통 stop-the-world의 시간을 줄이는 작업을 하는 것이다. 

### 2단계) Mark and Sweep
- Mark: `사용되는` 메모리(Reachable)와 `사용되지 않는` 메모리(Unreachable)를 식별하는 작업
- Sweep: Mark 단계에서 사용되지 않음으로 식별된 `메모리를 해제`하는 작업

## 4. Minor GC의 동작 방식
### 구조
![img_3.png](img_3.png)
- Eden 영역: `새로 생성된 객체`가 할당(Allocation)되는 영역 
- Survivor 영역(2개): 최소 `1번 이상의 GC에 살아남은 객체`가 존재하는 영역

### 동작 방식
![img_2.png](img_2.png)
1. 새로 생성된 객체가 Eden 영역에 할당된다.
2. 객체가 계속 생성되어 Eden 영역이 꽉차게 되고 Minor GC가 실행된다. 
   1. Eden 영역에서 사용되지 않는 객체의 메모리가 해제된다. 
   2. Eden 영역에서 살아남은 객체는 Survivor 영역 중 하나로 이동된다.
3. 만약, 하나의 Survivor 영역이 가득 차게 되면 그 중에서 살아남은 객체를 다른 Survivor 영역으로 이동한다. (즉, 하나의 Survivor 영역은 비어있는 상태)
4. 이러한 과정을 반복하여 계속해서 살아남은 객체는 Old 영역으로 이동된다.

> [ Old 영역으로의 이동(Promotion) ] <br>
> Minor GC는 객체의 생존 횟수(age)를 카운트 하여 Object Header에 기록하고 age를 통해 Promition 여부를 결정한다

### Bump-The-Pointer, TLABs(Thread-Local Allocation Buffers)
`HotSpot JVM`에서는 Eden 영역에 객체를 빠르게 할당(Allocation)하기 위해 사용되는 기술

`Bump-The-Pointer`란 Eden 영역에 마지막으로 할당된 객체의 주소를 캐싱해두는 것이다. 
이로 인해 새로운 객체에 메모리를 할당해 주기 위해 마지막으로 할당된 객체의 주소의 다음 주소를 사용하여 속도를 향상시킨다

멀티쓰레드 환경에서 마지막으로 할당된 객체의 주소에 접근하기 위해 `락(Lock)`을 걸어 `동기화`를 해주어야 한다. 
`TLABs(Thread-Local Allocation Buffers)`란 `각각의 쓰레드마다` Eden 영역에 객체를 할당하기 위한 주소를 부여함으로써 동기화 작업 없이 빠르게 메모리를 할당하도록 하는 기술이다.

## 5. Major GC 동작 방식
Old 영역이 가득 차면 Major GC를 실행한다


## 6. Minor GC vs Magor GC
| **GC 종류**  | **Minor GC**            | **Major GC**          |
|--------------|--------------------------|-----------------------|
| **대상**      | Young Generation        | Old Generation        |
| **실행 시점** | Eden 영역이 꽉 찬 경우 | Old 영역이 꽉 찬 경우 |
| **실행 속도** | 빠르다                  | 느리다                |
