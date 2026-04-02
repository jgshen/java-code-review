# 代码格式规则

## CF-001: IDE编码设置
**规则**：IDE编码必须设置为UTF-8
**严重程度**：严重
**检查逻辑**：项目编码设置
**正确示例**：
```xml
<!-- pom.xml -->
<properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
</properties>
```
**错误示例**：
```xml
<!-- 未设置编码，默认使用平台编码 -->
```
**修复建议**：在pom.xml中设置UTF-8编码

---

## CF-002: 缩进风格
**规则**：缩进使用4个空格，禁止使用tab字符
**严重程度**：警告
**检查逻辑**：检查代码中的tab字符
**正确示例**：
```java
public void method() {
    if (condition) {
        doSomething();
    }
}
```
**错误示例**：
```java
public void method() {
→   if (condition) {
→   →   doSomething();
→   }
}
```
**修复建议**：将tab替换为4个空格，IDE设置缩进为4个空格

---

## CF-003: 左大括号位置
**规则**：左大括号不换行，左圆括号与左大括号之间有一个空格
**严重程度**：警告
**检查逻辑**：检查左大括号是否独立成行
**正确示例**：
```java
public void method() {
    if (condition) {
        doSomething();
    }
}
```
**错误示例**：
```java
public void method()
{
    if (condition)
    {
        doSomething();
    }
}
```
**修复建议**：将左大括号放在同一行

---

## CF-004: 运算符空格
**规则**：运算符两侧各有一个空格，括号内不加空格
**严重程度**：警告
**检查逻辑**：检查运算符和括号周围的空格
**正确示例**：
```java
int result = a + b;
if (a > b && c < d) { }
method(a, b, c);
int[] arr = new int[]{1, 2, 3};
```
**错误示例**：
```java
int result = a+b;          // 缺少空格
if (a>b && c<d) { }       // 缺少空格
method( a, b, c );        // 括号内多余空格
```
**修复建议**：添加或移除相应位置的空格

---

## CF-005: 行长度限制
**规则**：单行代码长度不超过120个字符
**严重程度**：提示
**检查逻辑**：单行代码超过120个字符
**正确示例**：
```java
public void method() {
    String url = "https://example.com/api/v1/users/"
              + "?page=1&size=20";
}
```
**错误示例**：
```java
String url = "https://example.com/api/v1/users/?page=1&size=20&sort=name";  // 超过120字符
```
**修复建议**：将长行拆分成多行

---

## CF-006: 空行使用
**规则**：方法之间、逻辑块之间使用空行分隔
**严重程度**：提示
**检查逻辑**：检查方法之间是否缺少空行
**正确示例**：
```java
public void method1() {
    doSomething();
}

public void method2() {
    doOther();
}
```
**错误示例**：
```java
public void method1() {
    doSomething();
}
public void method2() {
    doOther();
}
```
**修复建议**：在方法之间添加空行

---

## CF-007: 花括号使用
**规则**：if/else/for/while/do语句即使只有一条语句也必须使用花括号
**严重程度**：警告
**检查逻辑**：单行语句未使用花括号
**正确示例**：
```java
if (condition) {
    doSomething();
}
```
**错误示例**：
```java
if (condition)
    doSomething();
```
**修复建议**：添加花括号

---

## CF-008: import组织顺序
**规则**：import语句按顺序排列：java/javax → 第三方 → com/alibaba → 内部类
**严重程度**：警告
**检查逻辑**：import语句顺序混乱
**正确示例**：
```java
import java.util.List;
import java.util.Map;

import org.springframework.stereotype.Service;

import com.alibaba.fastjson.JSON;

import com.example.common.BaseService;
```
**错误示例**：
```java
import com.example.common.BaseService;
import java.util.List;
import org.springframework.stereotype.Service;
```
**修复建议**：按规范顺序重新排列import语句

---

## CF-009: import不要使用通配符
**规则**：禁止import使用*通配符
**严重程度**：警告
**检查逻辑**：import语句使用*通配符
**正确示例**：
```java
import java.util.ArrayList;
import java.util.HashMap;
```
**错误示例**：
```java
import java.util.*;  // 通配符
```
**修复建议**：明确列出需要导入的类
