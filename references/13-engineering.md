# 工程规约

## EG-001: 应用分层
**规则**：应用分层为DO/DTO/VO/BO/Controller/Service/DAO等，各层职责明确
**严重程度**：警告
**检查逻辑**：分层不清晰，职责混乱
**正确示例**：
```
com.example.order
├── controller/OrderController.java   // 处理请求和响应
├── service/IOrderService.java        // 服务接口
├── service/impl/OrderServiceImpl.java // 服务实现
├── dao/IOrderDAO.java                // DAO接口
├── dao/impl/OrderDAOImpl.java       // DAO实现
├── do/OrderDO.java                   // Data Object，与数据库表一一对应
├── dto/OrderDTO.java                  // Data Transfer Object，传输数据
└── vo/OrderVO.java                    // View Object，展示数据
```
**错误示例**：
```
com.example.order
├── OrderController.java  // 混杂
├── OrderService.java     // 混杂业务逻辑和数据访问
├── OrderDAO.java
├── Order.java            // 不明确是DO还是VO
```
**修复建议**：按职责分层，区分DO/DTO/VO

---

## EG-002: DO/DTO/VO命名
**规则**：DO/BO/DTO/VO命名必须明确其类型后缀
**严重程度**：警告
**检查逻辑**：数据对象未使用DO/DTO/VO后缀
**正确示例**：
```java
public class UserDO { }    // Data Object
public class UserDTO { }   // Data Transfer Object
public class UserVO { }    // View Object
public class UserBO { }    // Business Object
public class UserQuery { } // 查询参数
```
**错误示例**：
```java
public class User { }      // 不明确类型
public class UserInfo { }  // 不明确类型
```
**修复建议**：添加DO/DTO/VO/BO/Query后缀

---

## EG-003: 二方库依赖
**规则**：二方库依赖格式为groupId:artifactId[:extension[:classifier]]:version
**严重程度**：警告
**检查逻辑**：依赖声明格式不规范
**正确示例**：
```xml
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>fastjson</artifactId>
    <version>1.2.76</version>
</dependency>
```
**错误示例**：
```xml
<dependency>
    <groupId>com.alibaba.fastjson</groupId>  -- 不应放在groupId中
</dependency>
```
**修复建议**：groupId和artifactId分开

---

## EG-004: 二方库分组
**规则**：二方库按领域分组，禁止无意义的聚合
**严重程度**：提示
**检查逻辑**：二方库分组不合理
**正确示例**：
```
com.example.order:order-api      // 订单领域API
com.example.order:order-dao      // 订单领域DAO
com.example.user:user-api        // 用户领域API
```
**错误示例**：
```
com.example.utils:util-string    // 工具类不应该单独分组
com.example.utils:util-date      // 工具类不应该单独分组
```
**修复建议**：按业务领域分组，避免过度拆分

---

## EG-005: 禁止循环依赖
**规则**：模块间禁止循环依赖
**严重程度**：严重
**检查逻辑**：模块间存在循环引用
**正确示例**：
```
module-a
├── service/AService.java
module-b
├── service/BService.java (依赖AService)
```
**错误示例**：
```
module-a
├── service/AService.java (依赖BService)
module-b
├── service/BService.java (依赖AService)  -- 循环依赖
```
**修复建议**：重构消除循环依赖，使用共同依赖层

---

## EG-006: 应用可运行内存
**规则**：应用必须设置可运行内存建议值
**严重程度**：警告
**检查逻辑**：JVM参数未设置
**正确示例**：
```bash
java -Xms4g -Xmx4g -XX:+UseG1GC -jar application.jar
```
**错误示例**：
```bash
java -jar application.jar  # 未设置JVM参数
```
**修复建议**：设置合理的堆内存大小和GC参数

---

## EG-007: 常量包结构
**规则**：常量按模块放在对应的constant包下
**严重程度**：提示
**检查逻辑**：常量分散在不合逻辑的位置
**正确示例**：
```
com.example.order
├── constant/
│   ├── OrderStatus.java
│   └── OrderType.java
com.example.user
├── constant/
│   └── UserType.java
```
**错误示例**：
```java
// OrderService中定义User常量
public class OrderService {
    public static final String USER_TYPE_ADMIN = "ADMIN";  // 不应在Order包中
}
```
**修复建议**：按模块组织常量

---

## EG-008: 配置文件格式
**规则**：配置文件使用yml/properties格式，禁止使用xml配置
**严重程度**：提示
**检查逻辑**：使用xml格式配置
**正确示例**：
```yaml
# application.yml
spring:
  datasource:
    url: jdbc:mysql://localhost:3306/test
```
**错误示例**：
```xml
<!-- application.xml -->
<beans>
    <bean id="dataSource"/>
</beans>
```
**修复建议**：使用yml或properties格式

---

## EG-009: API返回值规范
**规则**：API返回值必须包含状态码、消息和数据
**严重程度**：警告
**检查逻辑**：API直接返回数据
**正确示例**：
```java
public class Result<T> {
    private int code;
    private String message;
    private T data;
}
```
**错误示例**：
```java
public String getUser() {
    return userService.getUser();  // 直接返回数据
}
```
**修复建议**：使用统一的返回格式

---

## EG-010: POM版本管理
**规则**：POM中dependency版本必须声明在dependencyManagement中
**严重程度**：警告
**检查逻辑**：直接在dependencies中指定版本
**正确示例**：
```xml
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>fastjson</artifactId>
            <version>1.2.76</version>
        </dependency>
    </dependencies>
</dependencyManagement>
```
**错误示例**：
```xml
<dependencies>
    <dependency>
        <groupId>com.alibaba</groupId>
        <artifactId>fastjson</artifactId>
        <version>1.2.76</version>  -- 子模块不应指定版本
    </dependency>
</dependencies>
```
**修复建议**：在父POM的dependencyManagement中统一管理版本
