# 命名风格规则

## NC-001: 抽象类命名
**规则**：抽象类命名必须以Abstract或Base开头
**检查逻辑**：类声明为abstract且类名不以Abstract/Base开头
**正确示例**：
```java
public abstract class AbstractUserService { }
public abstract class BaseController { }
```
**错误示例**：
```java
public abstract class UserService { }
```
**修复建议**：将类名改为`AbstractUserService`或`BaseUserService`

---

## NC-002: 枚举类命名
**规则**：枚举类命名必须以Enum结尾
**检查逻辑**：枚举类名不以Enum结尾
**正确示例**：
```java
public enum StatusEnum { READY, RUNNING, FINISHED; }
public enum OrderTypeEnum { WEB, APP, WAP; }
```
**错误示例**：
```java
public enum Status { READY, RUNNING, FINISHED; }
public enum OrderType { WEB, APP, WAP; }
```
**修复建议**：将枚举类名改为`StatusEnum`、`OrderTypeEnum`

---

## NC-003: 常量命名
**规则**：常量（static final）必须全大写，单词间以下划线分隔
**检查逻辑**：静态常量字段名不符合`^[A-Z][A-Z0-9_]*$`模式
**正确示例**：
```java
public static final int MAX_RETRY_COUNT = 3;
private static final String DEFAULT_CHARSET = "UTF-8";
public static final long MAX_TIMEOUT_MILLIS = 5000L;
```
**错误示例**：
```java
public static final int maxRetryCount = 3;       // 小写
public static final String CHARSET = "UTF-8";    // 非全大写
private static final int retrycount = 3;        // 混用
```
**修复建议**：将常量名改为全大写格式

---

## NC-004: Boolean变量命名
**规则**：Boolean变量不应使用is、has、can、will前缀
**检查逻辑**：boolean类型字段名匹配`^(is|has|can|will)\w+`
**正确示例**：
```java
private boolean ready;       // 直接用形容词
private boolean loggedIn;    // 用过去分词
private boolean running;     // 用现在分词
```
**错误示例**：
```java
private boolean isReady;     // is前缀
private boolean hasLoggedIn; // has前缀
private boolean canAccess;   // can前缀
private boolean willRetry;   // will前缀
```
**修复建议**：移除is/has/can/will前缀，使用形容词或过去/现在分词

---

## NC-005: 包名命名
**规则**：包名必须全小写
**检查逻辑**：package声明中的包名包含大写字母
**正确示例**：
```java
package com.example.myproject;
package com.alibaba.druid.util;
```
**错误示例**：
```java
package com.Example.MyProject;  // 包含大写
package com阿里巴巴_util;        // 包含中文
```
**修复建议**：将包名改为全小写格式

---

## NC-006: 类名命名
**规则**：类名使用UpperCamelCase风格
**检查逻辑**：类名不符合`^[A-Z][a-zA-Z0-9]*$`模式
**正确示例**：
```java
public class UserService { }
public class OrderController { }
public class HttpRequestUtils { }
```
**错误示例**：
```java
public class userService { }      // 小写开头
public class order_service { }    // 包含下划线
public class User { }             // 太短无意义
```
**修复建议**：使用UpperCamelCase风格命名

---

## NC-007: 方法名命名
**规则**：方法名使用lowerCamelCase风格
**检查逻辑**：方法名不符合`^[a-z][a-zA-Z0-9]*$`模式
**正确示例**：
```java
public void getUserById(Long id) { }
public boolean isEmpty(String str) { }
public List<User> findAllActiveUsers() { }
```
**错误示例**：
```java
public void GetUserById(Long id) { }   // 大写开头
public void user_by_id(Long id) { }    // 下划线风格
```
**修复建议**：使用lowerCamelCase风格命名

---

## NC-008: 接口实现类命名
**规则**：接口实现类命名应以接口名加上Impl结尾
**检查逻辑**：实现接口的类名不以Impl结尾
**正确示例**：
```java
public interface UserService { }
public class UserServiceImpl implements UserService { }
```
**错误示例**：
```java
public interface UserService { }
public class UserServiceImplV1 implements UserService { }  // 可接受
public class UserServiceImpl2 implements UserService { }  // 数字结尾
```
**修复建议**：实现类命名为`接口名 + Impl`

---

## NC-009: 数组命名
**规则**：数组命名使用复数形式或添加Array后缀
**检查逻辑**：变量名为数组类型但未使用复数形式
**正确示例**：
```java
String[] userNames;
int[] scoreArray;
List<User> userList;
```
**错误示例**：
```java
String[] name;     // 单数形式
int[] userScores;  // 可接受但不清晰
```
**修复建议**：使用清晰的复数形式或添加Array后缀

---

## NC-010: 循环变量命名
**规则**：循环变量使用有意义的名称，避免单字母（除标准i/j/k外）
**检查逻辑**：循环变量名为单字母且非i/j/k
**正确示例**：
```java
for (int i = 0; i < size; i++) { }
for (int index = 0; index < size; index++) { }
for (User user : userList) { }
```
**错误示例**：
```java
for (int x = 0; x < size; x++) { }  // x含义不清
for (int a = 0; a < size; a++) { }
```
**修复建议**：使用有意义的循环变量名
