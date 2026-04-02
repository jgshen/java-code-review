# 常量定义规则

## CT-001: 禁止魔法值
**规则**：禁止在代码中直接使用未定义的字符串或数字常量（魔法值）
**严重程度**：严重
**检查逻辑**：
- 字符串字面量（3字符以上）需定义为常量
- 数字字面量（除-1, 0, 1, 2外）需定义为常量
**正确示例**：
```java
private static final String ORDER_STATUS_PAID = "PAID";
private static final int MAX_RETRY_COUNT = 3;
private static final int DEFAULT_PAGE_SIZE = 20;

public void process(String status) {
    if (ORDER_STATUS_PAID.equals(status)) { }
}
```
**错误示例**：
```java
public void process(String status) {
    if ("PAID".equals(status)) { }  // 魔法值
    for (int i = 0; i < 100; i++) { }  // 魔法值100
}
```
**修复建议**：将魔法值提取为private static final常量

---

## CT-002: Long类型后缀
**规则**：Long类型常量后缀必须使用大写L，禁止使用小写l
**严重程度**：严重
**检查逻辑**：Long类型字面量使用小写l后缀
**正确示例**：
```java
private static final long MAX_TIMEOUT = 5000L;
private static final long DEFAULT_ID = -1L;
public static final long serialVersionUID = 1L;
```
**错误示例**：
```java
private static final long MAX_TIMEOUT = 5000l;  // 小写l易与1混淆
private static final long DEFAULT_ID = -1l;
```
**修复建议**：将小写l改为大写L

---

## CT-003: 跨应用共享常量
**规则**：跨应用共享的常量放在二方库里，不允许定义在类中
**严重程度**：警告
**检查逻辑**：检查常量的作用域和共享范围
**正确示例**：
```java
// 在二方库 com.example:common-constants 中定义
package com.example.constant;
public class MyConstants {
    public static final String APP_NAME = "my-app";
}
```
**错误示例**：
```java
// 在应用代码中定义跨应用共享常量
public class UserService {
    public static final String APP_NAME = "my-app";  // 不应在应用中定义
}
```
**修复建议**：将跨应用常量移到二方库中

---

## CT-004: 接口常量定义
**规则**：接口中的常量必须以static final修饰，可省略不写
**严重程度**：警告
**检查逻辑**：接口中定义可变的字段
**正确示例**：
```java
public interface StatusEnum {
    String SUCCESS = "success";
    String FAIL = "fail";
    // 隐式 static final，推荐省略修饰符
}
```
**错误示例**：
```java
public interface StatusEnum {
    public static final String SUCCESS = "success";
    private static final String FAIL = "fail";  // 接口中private不合规
}
```
**修复建议**：接口中只定义static final常量

---

## CT-005: switch枚举常量
**规则**：switch的case枚举常量必须使用小写形式
**严重程度**：提示
**检查逻辑**：case中枚举常量使用大写形式
**正确示例**：
```java
switch (statusEnum) {
    case ready:    // 小写
        break;
    case running:  // 小写
        break;
}
```
**错误示例**：
```java
switch (statusEnum) {
    case READY:   // 大写
        break;
}
```
**修复建议**：枚举常量在case中使用小写形式

---

## CT-006: 循环体语句块
**规则**：if/for/while/do的执行语句块必须使用大括号
**严重程度**：警告
**检查逻辑**：if/for/while/do后直接跟单行语句
**正确示例**：
```java
if (condition) {
    doSomething();
}
```
**错误示例**：
```java
if (condition)
    doSomething();  // 单行语句风险
```
**修复建议**：使用大括号包围执行语句

---

## CT-007: 禁止单行if/for/while
**规则**：禁止if/for/while/do使用单行语句
**严重程度**：警告
**检查逻辑**：单行if/for/while语句
**正确示例**：
```java
if (condition) {
    count++;
}
```
**错误示例**：
```java
if (condition)
    count++;
```
**修复建议**：使用大括号，即使只有一行代码
