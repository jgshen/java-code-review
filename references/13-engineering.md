# 工程规约

## EG-001: 应用分层
**规则**：应用分层为DO/DTO/VO/BO/DTO/Query/Controller/Service/DAO等
**检查逻辑**：分层不清晰，职责混乱
**正确示例**：
```
com.example.order
├── controller/OrderController.java
├── service/OrderService.java
├── service/impl/OrderServiceImpl.java
├── dao/OrderDAO.java
├── do/OrderDO.java (Data Object)
├── dto/OrderDTO.java (Data Transfer Object)
└── vo/OrderVO.java (View Object)
```
**错误示例**：
```
com.example.order
├── OrderController.java  // 混杂
├── OrderService.java
├── OrderDAO.java
├── Order.java  // 不明确是DO还是VO
```
**修复建议**：按职责分层，区分DO/DTO/VO

---

## EG-002: DO/DTO/VO命名
**规则**：DO/BO/DTO/VO命名必须明确其类型后缀
**检查逻辑**：数据对象未使用DO/DTO/VO后缀
**正确示例**：
```java
public class UserDO { }  // Data Object
public class UserDTO { } // Data Transfer Object
public class UserVO { }  // View Object
public class UserBO { }  // Business Object
```
**错误示例**：
```java
public class User { }  // 不明确类型
public class UserInfo { }  // 不明确类型
```
**修复建议**：添加DO/DTO/VO/BO后缀

---

## EG-003: 二方库依赖
**规则**：二方库依赖格式为groupId:artifactId[:extension[:classifier]]:version
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

## EG-004: 禁止循环依赖
**规则**：模块间禁止循环依赖
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

## EG-005: 常量包结构
**规则**：常量按模块放在对应的constant包下
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

## EG-006: Server配置
**规则**：服务器配置需包含JVM参数、连接池配置等
**检查逻辑**：缺少生产环境配置
**正确示例**：
```properties
# JVM
-server
-Xms4g -Xmx4g
-XX:+UseG1GC
-XX:MaxGCPauseMillis=200

# 连接池
dubbo.protocol.threadpool=fixed
dubbo.protocol.threads=200
```
**错误示例**：
```properties
# 无JVM配置
# 无连接池配置
```
**修复建议**：添加完整的服务器配置

---

## EG-007: POM版本管理
**规则**：POM中dependency版本必须声明在dependencyManagement中
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

<dependencies>
    <dependency>
        <groupId>com.alibaba</groupId>
        <artifactId>fastjson</artifactId>
    </dependency>
</dependencies>
```
**错误示例**：
```xml
<dependencies>
    <dependency>
        <groupId>com.alibaba</groupId>
        <artifactId>fastjson</artifactId>
        <version>1.2.76</version>  -- 不应在子模块中指定版本
    </dependency>
</dependencies>
```
**修复建议**：在父POM的dependencyManagement中统一管理版本

---

## EG-008: 模块职责
**规则**：每个模块有且只有一个明确的职责
**检查逻辑**：模块职责不单一
**正确示例**：
```
module-user        -- 用户模块
module-order       -- 订单模块
module-payment     -- 支付模块
```
**错误示例**：
```
module-common      -- 什么都放
module-utils       -- 混杂各种无关工具
```
**修复建议**：按业务领域划分模块，每个模块职责单一
