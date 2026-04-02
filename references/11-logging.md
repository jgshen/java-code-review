# 日志规约

## LG-001: 日志对象定义
**规则**：日志对象必须使用private static final修饰
**检查逻辑**：Logger不是private static final
**正确示例**：
```java
private static final Logger logger = LoggerFactory.getLogger(UserService.class);
```
**错误示例**：
```java
public Logger logger = LoggerFactory.getLogger(UserService.class);  // 非private
private Logger logger = LoggerFactory.getLogger(UserService.class);  // 非static
```
**修复建议**：使用`private static final`修饰

---

## LG-002: 日志级别选择
**规则**：根据场景选择正确的日志级别
**检查逻辑**：日志级别使用不当
**正确示例**：
```java
logger.debug("Processing order: {}", orderId);  // 调试信息
logger.info("Order created: {}", orderId);     // 一般信息
logger.warn("Retry attempt {}/{}", attempt, maxAttempts);  // 警告
logger.error("Failed to process order", e);   // 错误
```
**错误示例**：
```java
logger.info("start process");  // 信息无意义
logger.debug("User login failed: {}", username);  // debug级别太低
```
**修复建议**：使用有意义的日志信息，选择合适的级别

---

## LG-003: 敏感信息保护
**规则**：禁止在日志中打印敏感信息（密码、密钥、身份证等）
**检查逻辑**：日志参数包含敏感关键词
**正确示例**：
```java
log.info("User login: name={}, maskedPassword=***", username);
log.info("Payment: amount={}, cardNo=***", amount);
```
**错误示例**：
```java
log.info("User login: password={}", password);  // 打印密码
log.info("Payment: cardNo={}", cardNo);  // 打印完整卡号
```
**修复建议**：打印脱敏后的信息

---

## LG-004: 日志参数占位符
**规则**：日志信息必须使用占位符，不使用字符串拼接
**检查逻辑**：使用+拼接日志参数
**正确示例**：
```java
logger.info("Order id: {}, status: {}", orderId, status);
```
**错误示例**：
```java
logger.info("Order id: " + orderId + ", status: " + status);  // 字符串拼接
```
**修复建议**：使用`{}`占位符

---

## LG-005: 异常日志打印
**规则**：打印异常时必须包含异常对象
**检查逻辑**：catch中打印异常但未传入异常对象
**正确示例**：
```java
} catch (Exception e) {
    logger.error("Operation failed", e);  // 正确
}
```
**错误示例**：
```java
} catch (Exception e) {
    logger.error("Operation failed");  // 缺少异常对象
}
```
**修复建议**：将异常e作为第二个参数传入

---

## LG-006: 应用异常不可吞掉
**规则**：应用异常不可被catch后不记录也不抛出
**检查逻辑**：catch块为空或只有无关操作
**正确示例**：
```java
try {
    // operation
} catch (ServiceException e) {
    logger.error("Service error", e);
    throw e;  // 重新抛出
}
```
**错误示例**：
```java
try {
    // operation
} catch (Exception e) {
    // 空的catch块
}
```
**修复建议**：至少记录日志或将异常抛出

---

## LG-007: 日志输出位置
**规则**：禁止System.out/System.err打印日志
**检查逻辑**：使用System.out.println或System.err.println
**正确示例**：
```java
logger.info("Application started");
```
**错误示例**：
```java
System.out.println("Application started");
System.err.println("Error occurred");
```
**修复建议**：使用日志框架输出

---

## LG-008: 日志文件配置
**规则**：生产环境必须配置日志文件输出，禁止只输出到控制台
**检查逻辑**：日志配置只包含console appender
**正确示例**：
```xml
<appender name="FILE" class="RollingFileAppender">
    <file>logs/application.log</file>
    <rollingPolicy class="TimeBasedRollingPolicy">
        <fileNamePattern>logs/application.%d{yyyy-MM-dd}.log</fileNamePattern>
    </rollingPolicy>
</appender>
```
**错误示例**：
```xml
<!-- 只有console appender -->
<appender name="CONSOLE" class="ConsoleAppender">
</appender>
```
**修复建议**：配置文件输出或使用日志聚合服务
