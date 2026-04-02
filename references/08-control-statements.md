# 控制语句规则

## CS-001: switch完整性
**规则**：switch语句必须包含default分支
**检查逻辑**：switch没有default
**正确示例**：
```java
switch (status) {
    case PENDING:
        handlePending();
        break;
    case DONE:
        handleDone();
        break;
    default:
        throw new IllegalArgumentException("Unknown status: " + status);
}
```
**错误示例**：
```java
switch (status) {
    case PENDING:
        handlePending();
        break;
    case DONE:
        handleDone();
        break;
    // 没有default
}
```
**修复建议**：添加default分支处理未知情况

---

## CS-002: switch穿透
**规则**：switch的case执行后必须有break或return
**检查逻辑**：case中缺少break/return导致穿透
**正确示例**：
```java
switch (type) {
    case A:
        doA();
        break;
    case B:
        doB();
        return;  // return也可以
    default:
        doDefault();
}
```
**错误示例**：
```java
switch (type) {
    case A:
        doA();  // 缺少break，会穿透到B
    case B:
        doB();
        break;
}
```
**修复建议**：每个case添加break/return

---

## CS-003: 条件布尔化
**规则**：禁止if条件是true/false
**检查逻辑**：if语句条件为true或false
**正确示例**：
```java
if (condition) {
    doSomething();
}
```
**错误示例**：
```java
if (true) {  // 或 false
    doSomething();
}
```
**修复建议**：使用实际的条件表达式

---

## CS-004: 复杂条件表达式
**规则**：复杂条件表达式应提取为boolean变量
**检查逻辑**：if条件超过3个逻辑运算符
**正确示例**：
```java
boolean isValid = order != null
    && order.getStatus() == Status.PAID
    && order.getAmount() > 0;

if (isValid) {
    process(order);
}
```
**错误示例**：
```java
if (order != null && order.getStatus() == Status.PAID && order.getAmount() > 0 && order.getCreateTime() > deadline) {
    // 条件过于复杂
}
```
**修复建议**：将复杂条件提取为有意义的boolean变量

---

## CS-005: 循环终止条件
**规则**：禁止在循环体内修改循环计数器
**检查逻辑**：循环体内修改for循环的i++
**正确示例**：
```java
for (int i = 0; i < list.size(); i++) {
    process(list.get(i));
}
```
**错误示例**：
```java
for (int i = 0; i < list.size(); i++) {
    if (condition) {
        i++;  // 不要修改循环计数器
    }
}
```
**修复建议**：使用额外的标志变量或重构循环逻辑

---

## CS-006: return与finally
**规则**：finally中不执行return
**检查逻辑**：finally块中有return语句
**正确示例**：
```java
try {
    return resource.getData();
} finally {
    resource.close();  // 不要return
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
**修复建议**：finally中只做清理，不要return

---

## CS-007: 条件嵌套
**规则**：避免多层条件嵌套，使用卫语句提前返回
**检查逻辑**：if嵌套超过3层
**正确示例**：
```java
public void process(Order order) {
    if (order == null) {
        return;
    }

    if (!order.isValid()) {
        return;
    }

    // 业务逻辑
}
```
**错误示例**：
```java
public void process(Order order) {
    if (order != null) {
        if (order.isValid()) {
            if (order.getAmount() > 0) {
                // 业务逻辑
            }
        }
    }
}
```
**修复建议**：使用卫语句提前返回，减少嵌套

---

## CS-008: try-catch范围
**规则**：try-catch中的代码范围应尽可能小
**检查逻辑**：try块包含过多不相关的操作
**正确示例**：
```java
String id = extractId(input);
User user = userService.getById(id);
```
**错误示例**：
```java
try {
    String input = readInput();
    String id = parseId(input);
    User user = userService.getById(id);
    order.setUser(user);
    orderService.save(order);
} catch (Exception e) {
    // 将不相关的异常一起捕获
}
```
**修复建议**：缩小try块范围，只包围可能异常的代码
