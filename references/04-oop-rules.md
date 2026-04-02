# OOP规约

## OP-001: 静态方法调用
**规则**：静态方法通过类名调用，不通过实例调用
**严重程度**：严重
**检查逻辑**：发现形如`instance.staticMethod()`的调用
**正确示例**：
```java
public class StringUtils {
    public static boolean isEmpty(String str) {
        return str == null || str.isEmpty();
    }
}

// 调用
boolean empty = StringUtils.isEmpty(str);
```
**错误示例**：
```java
StringUtils utils = new StringUtils();
boolean empty = utils.isEmpty(str);  // 通过实例调用静态方法
```
**修复建议**：改为`类名.静态方法()`调用

---

## OP-002: 线程封闭
**规则**：多线程操作共享变量时，必须使用ThreadLocal封闭数据
**严重程度**：严重
**检查逻辑**：多个线程访问共享可变变量
**正确示例**：
```java
public class UserContext {
    private static final ThreadLocal<User> currentUser = new ThreadLocal<>();

    public static void setUser(User user) {
        currentUser.set(user);
    }

    public static User getUser() {
        return currentUser.get();
    }
}
```
**错误示例**：
```java
public class UserContext {
    private User currentUser;  // 共享可变状态

    public void setUser(User user) {
        this.currentUser = user;  // 多线程不安全
    }
}
```
**修复建议**：使用ThreadLocal封装可变状态

---

## OP-003: Object.equals
**规则**：Object的equals方法容易产生NPE，应使用Objects.equals()
**严重程度**：严重
**检查逻辑**：直接调用object.equals(常量或方法)
**正确示例**：
```java
import java.util.Objects;

if (Objects.equals(str, "expected")) { }
if (Objects.equals(a, b)) { }
```
**错误示例**：
```java
if (str.equals("expected")) { }  // str可能为NPE
if (obj.method().equals("value")) { }  // 方法可能返回null
```
**修复建议**：使用`Objects.equals(a, b)`替代`a.equals(b)`

---

## OP-004: POJO类float/double
**规则**：POJO类属性不能为float/double，应使用BigDecimal
**严重程度**：严重
**检查逻辑**：POJO类使用float或double类型
**正确示例**：
```java
public class Product {
    private BigDecimal price;  // 使用BigDecimal
    private BigDecimal discount;
}
```
**错误示例**：
```java
public class Product {
    private double price;      // float/double精度问题
    private float discount;
}
```
**修复建议**：使用BigDecimal替代float/double

---

## OP-005: 构造方法禁止业务逻辑
**规则**：构造方法禁止包含任何业务逻辑
**严重程度**：严重
**检查逻辑**：构造方法中存在复杂逻辑调用
**正确示例**：
```java
public class User {
    private String name;

    public User(String name) {
        this.name = name;  // 仅赋值
    }
}
```
**错误示例**：
```java
public class User {
    private String name;
    private String password;

    public User(String name) {
        this.name = name;
        this.password = passwordEncoder.encode(name);  // 业务逻辑
        init();  // 业务逻辑调用
    }
}
```
**修复建议**：将业务逻辑移到单独的方法中

---

## OP-006: 工具类禁止实例化
**规则**：工具类不允许被实例化，构造函数需private
**严重程度**：严重
**检查逻辑**：工具类构造函数为public
**正确示例**：
```java
public final class StringUtils {
    private StringUtils() {
        throw new Error("禁止实例化");
    }
}
```
**错误示例**：
```java
public final class StringUtils {
    public StringUtils() { }  // 可以实例化
}
```
**修复建议**：构造函数private并抛出Error

---

## OP-007: 禁止重复赋值
**规则**：同一个方法不允许对同一变量进行多次赋值
**严重程度**：警告
**检查逻辑**：同一变量被多次赋值
**正确示例**：
```java
public BigDecimal calculate(BigDecimal amount) {
    BigDecimal result = amount;
    result = result.multiply(rate);  // 只赋值一次
    return result;
}
```
**错误示例**：
```java
public BigDecimal calculate(BigDecimal amount) {
    BigDecimal result = amount;
    result = result.multiply(rate);
    result = result.add(fee);  // 重复赋值
    return result;
}
```
**修复建议**：使用临时变量或重构方法

---

## OP-008: equals和hashCode
**规则**：equals方法覆写时必须同时覆写hashCode方法
**严重程度**：严重
**检查逻辑**：覆写了equals但未覆写hashCode的类
**正确示例**：
```java
@Override
public boolean equals(Object o) {
    if (this == o) return true;
    if (o == null || getClass() != o.getClass()) return false;
    User user = (User) o;
    return Objects.equals(id, user.id);
}

@Override
public int hashCode() {
    return Objects.hash(id);
}
```
**错误示例**：
```java
@Override
public boolean equals(Object o) {
    // equals实现
}
// 未覆写hashCode
```
**修复建议**：在覆写equals时同时覆写hashCode

---

## OP-009: 外部可变对象
**规则**：禁止在类中返回可变对象的引用
**严重程度**：严重
**检查逻辑**：返回List/Map/Set等可变集合的getter
**正确示例**：
```java
public class UserService {
    private List<String> roles = new ArrayList<>();

    public List<String> getRoles() {
        return Collections.unmodifiableList(roles);
    }
}
```
**错误示例**：
```java
public List<String> getRoles() {
    return roles;  // 直接返回可变集合的引用
}
```
**修复建议**：返回不可变拷贝或使用Collections.unmodifiableList

---

## OP-010: 覆写方法注解
**规则**：覆写方法必须使用@Override注解
**严重程度**：警告
**检查逻辑**：方法声明与父类方法签名相同但未标注@Override
**正确示例**：
```java
@Override
public void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
}
```
**错误示例**：
```java
public void onCreate(Bundle savedInstanceState) {  // 缺少@Override
    super.onCreate(savedInstanceState);
}
```
**修复建议**：添加@Override注解

---

## OP-011: 构造方法参数校验
**规则**：构造方法参数必须校验，非法参数应抛出IllegalArgumentException
**严重程度**：严重
**检查逻辑**：构造方法缺少参数校验
**正确示例**：
```java
public User(Long id, String name) {
    if (id == null || id <= 0) {
        throw new IllegalArgumentException("id must be positive");
    }
    if (name == null || name.isEmpty()) {
        throw new IllegalArgumentException("name cannot be empty");
    }
    this.id = id;
    this.name = name;
}
```
**错误示例**：
```java
public User(Long id, String name) {
    this.id = id;  // 未校验id
    this.name = name;  // 未校验name
}
```
**修复建议**：在构造方法开始处添加参数校验
