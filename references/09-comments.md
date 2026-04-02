# 注释规约

## CM-001: 类注释
**规则**：类必须包含@author和@version注释
**严重程度**：警告
**检查逻辑**：public类缺少类注释或注释中无@author/@version
**正确示例**：
```java
/**
 * 用户服务类
 *
 * @author zhangsan
 * @version 1.0.0
 * @since 2024-01-01
 */
public class UserService {
}
```
**错误示例**：
```java
// 用户服务类
public class UserService {
}
```
**修复建议**：添加Javadoc注释，包含@author和@version

---

## CM-002: serialVersionUID
**规则**：实现了Serializable的类必须显式定义serialVersionUID
**严重程度**：警告
**检查逻辑**：实现了Serializable但未定义serialVersionUID
**正确示例**：
```java
public class User implements Serializable {
    private static final long serialVersionUID = 1L;
}
```
**错误示例**：
```java
public class User implements Serializable {
    // 缺少serialVersionUID
}
```
**修复建议**：添加`private static final long serialVersionUID = 1L;`

---

## CM-003: 方法注释
**规则**：重要方法必须包含Javadoc注释
**严重程度**：提示
**检查逻辑**：public方法缺少Javadoc注释
**正确示例**：
```java
/**
 * 根据ID获取用户
 *
 * @param id 用户ID
 * @return 用户对象，不存在返回null
 * @throws IllegalArgumentException id为空或负数
 */
public User getById(Long id) {
    // ...
}
```
**错误示例**：
```java
public User getById(Long id) {
    // ...
}
```
**修复建议**：添加Javadoc注释说明参数、返回值和异常

---

## CM-004: 注释与代码一致
**规则**：注释必须与代码保持一致，代码变更后注释必须同步更新
**严重程度**：警告
**检查逻辑**：注释描述与实际代码逻辑不符
**正确示例**：
```java
// 当订单状态为已支付时，标记为发货
if (OrderStatus.PAID.equals(order.getStatus())) {
    order.setStatus(OrderStatus.SHIPPED);
}
```
**错误示例**：
```java
// 当订单状态为已发货时，标记为发货（注释错误）
if (OrderStatus.PAID.equals(order.getStatus())) {
    order.setStatus(OrderStatus.SHIPPED);
}
```
**修复建议**：更新注释使其与代码一致

---

## CM-005: 删除无用注释
**规则**：删除无用的注释代码
**严重程度**：提示
**检查逻辑**：被注释掉的代码长期存在
**正确示例**：
```java
// 使用新版实现
public void process() {
    newProcess();
}
```
**错误示例**：
```java
// 旧的实现方式，已废弃
// public void oldProcess() {
//     // ...
// }
public void newProcess() {
}
```
**修复建议**：删除注释掉的代码，使用版本控制保存历史

---

## CM-006: TODO注释
**规则**：TODO注释必须说明原因和计划时间
**严重程度**：提示
**检查逻辑**：TODO注释无意义
**正确示例**：
```java
// TODO(zhangsan): 当支付渠道上线后替换为真实实现 2024-06-01
```
**错误示例**：
```java
// TODO: fix this later
```
**修复建议**：TODO格式：`// TODO(username): 描述 时间`

---

## CM-007: 注释位置
**规则**：注释应放在被注释代码上方，不放在行尾
**严重程度**：提示
**检查逻辑**：行尾注释与代码同行
**正确示例**：
```java
// 用户ID
private Long userId;

// 用户名称
private String userName;
```
**错误示例**：
```java
private Long userId;  // 用户ID
private String userName;  // 用户名称
```
**修复建议**：将注释移到代码上方

---

## CM-008: 注释内容质量
**规则**：注释内容要有意义，不是废话
**严重程度**：提示
**检查逻辑**：无意义的注释
**正确示例**：
```java
// 检查用户是否存在
if (user == null) {
    throw new UserNotFoundException();
}
```
**错误示例**：
```java
// if user is null
if (user == null) {  // 废话注释
    throw new UserNotFoundException();
}
```
**修复建议**：注释应解释"为什么"而非"是什么"
