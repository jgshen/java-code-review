# 命名风格规则

## NC-001: 抽象类命名
**规则**：抽象类命名必须以Abstract或Base开头
**严重程度**：警告
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
**严重程度**：警告
**检查逻辑**：枚举类名不以Enum结尾
**正确示例**：
```java
public enum StatusEnum { READY, RUNNING, FINISHED; }
public enum OrderTypeEnum { WEB, APP, WAP; }
```
**错误示例**：
```java
public enum Status { READY, RUNNING, FINISHED; }
```
**修复建议**：将枚举类名改为`StatusEnum`、`OrderTypeEnum`

---

## NC-003: 常量命名
**规则**：常量（static final）必须全大写，单词间以下划线分隔
**严重程度**：警告
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
**严重程度**：警告
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
```
**修复建议**：移除is/has/can/will前缀，使用形容词或过去/现在分词

---

## NC-005: 包名命名
**规则**：包名必须全小写
**严重程度**：严重
**检查逻辑**：package声明中的包名包含大写字母
**正确示例**：
```java
package com.example.myproject;
package com.alibaba.druid.util;
```
**错误示例**：
```java
package com.Example.MyProject;  // 包含大写
```
**修复建议**：将包名改为全小写格式

---

## NC-006: 接口命名
**规则**：接口类名不加I前缀，接口和实现类命名规范
**严重程度**：警告
**检查逻辑**：接口名以I开头
**正确示例**：
```java
public interface UserService { }
public interface PersonService { }
public class UserServiceImpl implements UserService { }
```
**错误示例**：
```java
public interface IUserService { }  // I前缀
public interface IpersonService { }  // 小写I开头
```
**修复建议**：移除I前缀，使用驼峰命名

---

## NC-007: Service/DAO命名
**规则**：Service/DAO层接口命名规范
**严重程度**：警告
**检查逻辑**：Service/DAO命名不符合规范
**正确示例**：
```java
// Service接口
public interface UserService { }
// Service实现
public class UserServiceImpl implements UserService { }
// DAO接口
public interface UserDAO { }
// DAO实现
public class UserDAOImpl implements UserDAO { }
```
**错误示例**：
```java
public interface UserBo { }  // 非标准后缀
public interface UserDao { }  // 小写dao
```
**修复建议**：使用标准后缀Service/DAO

---

## NC-008: 聚合类命名
**规则**：聚合类用Tool/Util结尾，集合类用Node/Element/Item结尾
**严重程度**：提示
**检查逻辑**：聚合类命名不符合规范
**正确示例**：
```java
public class StringUtil { }    // 工具类
public class CollectionUtil { }
public class OrderItem { }     // 集合元素
public class TreeNode { }
```
**错误示例**：
```java
public class StringUtils { }  // Utils结尾不推荐
```
**修复建议**：使用Tool/Util结尾

---

## NC-009: 常量类分组
**规则**：相关常量应放在同一常量类中
**严重程度**：提示
**检查逻辑**：相关常量分散在多个类中
**正确示例**：
```java
public class OrderConstants {
    public static final String STATUS_PAID = "PAID";
    public static final String STATUS_SHIPPED = "SHIPPED";
}
```
**错误示例**：
```java
public class UserService {
    public static final String ORDER_STATUS = "PAID";  // 不应在Service中定义Order常量
}
```
**修复建议**：按功能模块组织常量类

---

## NC-010: POJO类属性命名
**规则**：POJO类属性使用包装类型，不使用基本类型
**严重程度**：警告
**检查逻辑**：POJO类使用int/long等基本类型
**正确示例**：
```java
public class User {
    private Integer id;        // 包装类型
    private Long orderId;      // 包装类型
    private Boolean active;    // 包装类型
}
```
**错误示例**：
```java
public class User {
    private int id;            // 基本类型，无法表示null
    private long orderId;
}
```
**修复建议**：使用Integer/Long/Boolean等包装类型
