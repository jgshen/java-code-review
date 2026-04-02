# 异常处理规则

## EH-001: 禁止捕获RuntimeException
**规则**：不可捕获RuntimeException、Error及其子类
**检查逻辑**：catch子句包含RuntimeException/Error/Throwable
**正确示例**：
```java
try {
    // 可能抛出检查型异常的操作
} catch (IOException e) {
    throw new BusinessException("IO error", e);
}
```
**错误示例**：
```java
try {
    // risky operation
} catch (RuntimeException e) {
    // 不应捕获RuntimeException
}
```
**修复建议**：捕获具体检查型异常，让RuntimeException传播

---

## EH-002: finally块return
**规则**：finally块中不可使用return语句
**检查逻辑**：finally块包含return
**正确示例**：
```java
try {
    return resource.close();
} finally {
    // 只做清理，不return
    if (resource != null) {
        resource.cleanup();
    }
}
```
**错误示例**：
```java
try {
    return resource.getData();
} finally {
    return resource.close();  // 错误！覆盖try中的return
}
```
**修复建议**：finally中不要return

---

## EH-003: 异常链
**规则**：捕获异常后必须抛出有意义的新异常，禁止直接throw e
**检查逻辑**：catch块中直接throw e
**正确示例**：
```java
try {
    // operation
} catch (IOException e) {
    throw new BusinessException("Operation failed", e);  // 提供上下文
}
```
**错误示例**：
```java
try {
    // operation
} catch (IOException e) {
    throw e;  // 直接抛出，无上下文
}
```
**修复建议**：抛出新异常并保留原异常作为cause

---

## EH-004: 应用异常不可吞掉
**规则**：应用异常不可被catch后不处理也不抛出
**检查逻辑**：catch块为空或只有无关操作
**正确示例**：
```java
try {
    // operation
} catch (DataAccessException e) {
    log.error("Database error", e);
    throw e;  // 或有明确处理
}
```
**错误示例**：
```java
try {
    // operation
} catch (Exception e) {
    // 空catch块，异常被吞掉
}
```
**修复建议**：至少记录日志或将异常重新抛出

---

## EH-005: 异常匹配顺序
**规则**：catch子句按从具体到宽泛的顺序排列
**检查逻辑**：catch顺序不正确
**正确示例**：
```java
try {
    // operation
} catch (FileNotFoundException e) {
    // 处理文件未找到
} catch (IOException e) {
    // 处理IO错误
} catch (Exception e) {
    // 处理其他异常
}
```
**错误示例**：
```java
try {
    // operation
} catch (Exception e) {  // 宽泛异常在前
    // ...
} catch (IOException e) {  // 永远不会执行
    // ...
}
```
**修复建议**：按从具体到宽泛的顺序排列catch子句

---

## EH-006: 异常信息完整性
**规则**：抛出异常时必须包含有意义的错误信息
**检查逻辑**：new Exception()无参数或无信息
**正确示例**：
```java
throw new IllegalArgumentException("用户ID不能为空");
throw new BusinessException("订单不存在, id=" + orderId, e);
```
**错误示例**：
```java
throw new RuntimeException();  // 无信息
throw new RuntimeException(e);  // 无描述信息
```
**修复建议**：提供有意义的错误信息

---

## EH-007: finally清理资源
**规则**：finally中必须释放资源，使用try-with-resources更佳
**检查逻辑**：获取的资源未在finally中关闭
**正确示例**：
```java
Connection conn = null;
try {
    conn = dataSource.getConnection();
    // operation
} finally {
    if (conn != null) {
        try {
            conn.close();
        } catch (SQLException e) {
            log.error("Close connection failed", e);
        }
    }
}

// 推荐使用try-with-resources
try (Connection conn = dataSource.getConnection()) {
    // operation
}
```
**错误示例**：
```java
Connection conn = dataSource.getConnection();
// operation
// 未关闭连接
```
**修复建议**：使用try-with-resources或finally中关闭

---

## EH-008: 检查型异常处理
**规则**：检查型异常必须被捕获或声明抛出
**检查逻辑**：未声明throws的检查型异常
**正确示例**：
```java
public void readFile() throws IOException {
    BufferedReader reader = new BufferedReader(new FileReader("file.txt"));
    // ...
}
```
**错误示例**：
```java
public void readFile() {  // 缺少throws声明
    BufferedReader reader = new BufferedReader(new FileReader("file.txt"));
    // ...
}
```
**修复建议**：在方法签名声明throws或捕获异常
