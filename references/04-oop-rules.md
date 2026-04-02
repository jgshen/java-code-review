# OOP规约

## OP-001: 静态方法调用
**规则**：静态方法通过类名调用，不通过实例调用
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

## OP-003: 继承与组合
**规则**：禁止使用继承来定义类，应使用组合
**检查逻辑**：类继承关系是否为"滥用继承"
**正确示例**：
```java
public class UserService {
    private UserDAO userDAO;  // 组合

    public void save(User user) {
        userDAO.insert(user);
    }
}
```
**错误示例**：
```java
public class UserService extends BaseService {  // 滥用继承
    public void save(User user) {
        // ...
    }
}
```
**修复建议**：优先使用组合而非继承

---

## OP-004: 覆写方法注解
**规则**：覆写方法必须使用@Override注解
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

## OP-005: 外部可变对象
**规则**：禁止在类中返回可变对象的引用
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

## OP-006: equals与hashCode
**规则**：equals方法覆写时必须同时覆写hashCode方法
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
```
**修复建议**：在覆写equals时同时覆写hashCode

---

## OP-007: 构造方法参数校验
**规则**：构造方法参数必须校验，非法参数应抛出IllegalArgumentException
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
