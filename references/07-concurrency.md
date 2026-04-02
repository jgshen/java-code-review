# 并发处理规则

## CC-001: 线程池命名
**规则**：创建线程或线程池必须指定有意义的线程名
**检查逻辑**：new Thread()或new ThreadPoolExecutor()未设置name
**正确示例**：
```java
ThreadFactory factory = new ThreadFactoryBuilder()
    .setNameFormat("user-consumer-%d")
    .build();

ThreadPoolExecutor executor = new ThreadPoolExecutor(
    2, 4, 60, TimeUnit.SECONDS,
    new LinkedBlockingQueue<>(100),
    factory
);
```
**错误示例**：
```java
ThreadPoolExecutor executor = new ThreadPoolExecutor(
    2, 4, 60, TimeUnit.SECONDS,
    new LinkedBlockingQueue<>(100)
    // 未指定ThreadFactory，默认线程名无意义
);
```
**修复建议**：使用ThreadFactoryBuilder设置有意义的线程名

---

## CC-002: 线程池配置
**规则**：线程池必须设置饱和策略
**检查逻辑**：ThreadPoolExecutor未设置饱和策略
**正确示例**：
```java
ThreadPoolExecutor executor = new ThreadPoolExecutor(
    corePoolSize, maximumPoolSize, keepAliveTime, TimeUnit.SECONDS,
    workQueue,
    Executors.defaultThreadFactory(),
    new ThreadPoolExecutor.AbortPolicy()  // 或CallerRunsPolicy等
);
```
**错误示例**：
```java
ThreadPoolExecutor executor = new ThreadPoolExecutor(
    corePoolSize, maximumPoolSize, keepAliveTime, TimeUnit.SECONDS,
    workQueue
    // 未指定饱和策略，默认抛出RejectedExecutionException
);
```
**修复建议**：显式指定饱和策略

---

## CC-003: 线程协作
**规则**：Thread.sleep()必须捕获或声明InterruptedException
**检查逻辑**：未处理InterruptedException
**正确示例**：
```java
try {
    Thread.sleep(1000);
} catch (InterruptedException e) {
    Thread.currentThread().interrupt();  // 恢复中断状态
}
```
**错误示例**：
```java
Thread.sleep(1000);  // 未捕获异常
```
**修复建议**：捕获InterruptedException并恢复中断状态

---

## CC-004: volatile使用
**规则**：只有一个线程写、其他线程读的变量需要加volatile
**检查逻辑**：存在可见性问题但未使用volatile
**正确示例**：
```java
private volatile boolean initialized = false;

public void init() {
    initialized = true;
}

public boolean isInitialized() {
    return initialized;
}
```
**错误示例**：
```java
private boolean initialized = false;  // 无volatile
// 多线程访问可能看到过期值
```
**修复建议**：对可见性有要求的变量添加volatile

---

## CC-005: 同步锁对象
**规则**：同步块的锁对象必须是最终对象，禁止使用Lock对象
**检查逻辑**：同步块使用非final或非规范对象作为锁
**正确示例**：
```java
private final Object lock = new Object();

public void method() {
    synchronized (lock) {
        // 同步操作
    }
}
```
**错误示例**：
```java
private String lock = "LOCK";  // String是常量池共享
synchronized (lock) { }  // 可能与其他代码冲突

private final Lock lock = new ReentrantLock();  // Lock不能用于synchronized
```
**修复建议**：使用private final Object作为锁对象

---

## CC-006: 死锁避免
**规则**：多个线程获取多个锁时必须按固定顺序获取
**检查逻辑**：检测可能的死锁模式
**正确示例**：
```java
// 始终按固定顺序获取锁
public void method(Account a, Account b) {
    if (a.getId() < b.getId()) {
        synchronized (a) {
            synchronized (b) {
                // 操作
            }
        }
    } else {
        synchronized (a) {
            synchronized (b) {
                // 操作
            }
        }
    }
}
```
**错误示例**：
```java
public void method(Account a, Account b) {
    synchronized (a) {
        synchronized (b) {  // 可能与另一个线程形成死锁
            // 操作
        }
    }
}
```
**修复建议**：按固定顺序获取锁，或使用java.util.concurrent.locks.Lock

---

## CC-007: 线程安全集合
**规则**：多线程环境下使用线程安全集合
**检查逻辑**：ArrayList/HashMap等非线程安全集合在多线程中使用
**正确示例**：
```java
List<String> list = new CopyOnWriteArrayList<>();
Map<String, String> map = new ConcurrentHashMap<>();
Set<String> set = new ConcurrentHashSet<>();
```
**错误示例**：
```java
List<String> list = new ArrayList<>();  // 非线程安全
// 在多线程环境中add操作可能导致数据丢失
```
**修复建议**：使用java.util.concurrent包下的线程安全集合
