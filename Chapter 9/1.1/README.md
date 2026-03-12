## 9.1 스레드 락 모니터링

- 스레드락은 멀티스레드 아키텍처에서 이벤트 흐름을 제어하기 위해 구현한 여러가지 스레드 동기화 장치때문에 발생한다.
- 스레드락은 앱에서 스레드를 제어하는 장치라서 필요하다. 시간을 최소화하여 앱 효율을 높이는 방법으로 가야한다.

1. 프로듀서 (스레드)는 값을 List에 추가한다.
2. 컨슈머 (스레드)는 값을 삭제한다.

```java
public class Consumer extends Thread {

  private Logger log = Logger.getLogger(Consumer.class.getName());

  public Consumer(String name) {
    super(name);
  }

  @Override
  public void run() {
    for (int i = 0; i < 1_000_000; i++) {
      synchronized (Main.list) {
        if (Main.list.size() > 0) {
          int x = Main.list.get(0);
          Main.list.remove(0);
          log.info("Consumer " + Thread.currentThread().getName() + " removed value " + x);
        }
      }
    }
  }
}
```

```java
public class Producer extends Thread {

  private Logger log = Logger.getLogger(Producer.class.getName());

  public Producer(String name) {
    super(name);
  }

  @Override
  public void run() {
    Random r = new Random();
    for (int i = 0; i < 1_000_000; i++) {
      synchronized (Main.list) {
        if (Main.list.size() < 100) {
          int x = r.nextInt();
          Main.list.add(x);
          log.info("Producer " + Thread.currentThread().getName() + " added value " + x);
        }
      }
    }
  }
}
```

- 컨슈머 스레드는 특정 코드를 백만번 반복한다. 로직이 구현된 전체 코드 블록은 List 인스턴스 자체를 모니터 삼아 동기화 한다. 동기화 블럭은 여러 스레드가 동시에 들어갈 수 없다.
- 프로듀서는 컨슈머와 비슷하다. 100미만인 경우에만 새 값을 추가한다.
- 여러 스레드 중 하나만 동기화한 블록에 들어가도록 허용하며, 이 블록을 끝까지 실행할 때까지 다른 스레드는 코드 블록 시작 지점에서 대기한다.
- 스레드는 동기화 코드 블록에 차단되거나, 다른 스레드가 완료되기를 기다린다. 스레드가 차단되어 더이상 무언갈 할 수 없는데, 이것을 **락** 이라고 한다.

> 책에는 예제로 프로파일링이 나와있지만, 지금 상황에서 할 수 없어서 스킵하겠다. 페이지는 188페이지이다.