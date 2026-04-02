# MySQL规约

## MS-001: 建表命名
**规则**：表名和字段名使用小写字母，单词间用下划线分隔
**严重程度**：严重
**检查逻辑**：表名包含大写字母或特殊字符
**正确示例**：
```sql
CREATE TABLE user_order (
    id BIGINT,
    user_id BIGINT,
    order_status VARCHAR(32)
);
```
**错误示例**：
```sql
CREATE TABLE userOrder (  -- 驼峰命名
    id BIGINT,
    userId BIGINT,
    orderStatus VARCHAR(32)
);
```
**修复建议**：使用小写字母加下划线命名

---

## MS-002: 布尔字段命名
**规则**：布尔型字段不得使用is_xxx命名
**严重程度**：严重
**检查逻辑**：字段名为is_xxx且类型为tinyint/boolean
**正确示例**：
```sql
CREATE TABLE user (
    id BIGINT,
    active TINYINT(1) DEFAULT 1 COMMENT '状态:0禁用,1启用',
    deleted TINYINT(1) DEFAULT 0 COMMENT '是否删除'
);
```
**错误示例**：
```sql
CREATE TABLE user (
    id BIGINT,
    is_active TINYINT(1),     -- is前缀
    is_deleted TINYINT(1)     -- is前缀
);
```
**修复建议**：移除is前缀，使用active/deleted等命名

---

## MS-003: 字段NOT NULL
**规则**：字段必须设置为NOT NULL并提供默认值
**严重程度**：警告
**检查逻辑**：字段没有NOT NULL约束
**正确示例**：
```sql
CREATE TABLE user (
    id BIGINT NOT NULL COMMENT '用户ID',
    name VARCHAR(64) NOT NULL DEFAULT '' COMMENT '用户名',
    age INT DEFAULT 0 COMMENT '年龄'
);
```
**错误示例**：
```sql
CREATE TABLE user (
    id BIGINT,
    name VARCHAR(64),
    age INT  -- 可以为NULL
);
```
**修复建议**：字段设置为NOT NULL并提供默认值

---

## MS-004: Decimal代替DOUBLE
**规则**：用于表示价格必须使用DECIMAL，禁止使用DOUBLE
**严重程度**：严重
**检查逻辑**：价格字段使用DOUBLE类型
**正确示例**：
```sql
CREATE TABLE product (
    id BIGINT,
    price DECIMAL(10,2) NOT NULL DEFAULT 0 COMMENT '价格',
    discount DECIMAL(10,2) DEFAULT 0 COMMENT '折扣'
);
```
**错误示例**：
```sql
CREATE TABLE product (
    id BIGINT,
    price DOUBLE NOT NULL COMMENT '价格'  -- DOUBLE精度问题
);
```
**修复建议**：使用DECIMAL(10,2)替代DOUBLE

---

## MS-005: 主键命名
**规则**：主键命名为pk_xxx或使用业务无关的自增主键
**严重程度**：警告
**检查逻辑**：复合主键使用业务相关字段
**正确示例**：
```sql
CREATE TABLE user_order (
    id BIGINT AUTO_INCREMENT PRIMARY KEY COMMENT '主键ID',
    user_id BIGINT NOT NULL COMMENT '用户ID',
    order_no VARCHAR(32) NOT NULL COMMENT '订单号'
);
```
**错误示例**：
```sql
CREATE TABLE user_order (
    user_id BIGINT NOT NULL,
    order_no VARCHAR(32) NOT NULL,
    PRIMARY KEY (user_id, order_no)  -- 业务相关复合主键
);
```
**修复建议**：使用自增主键，业务字段加唯一索引

---

## MS-006: 索引命名
**规则**：索引命名为idx_xxx、uniq_xxx
**严重程度**：警告
**检查逻辑**：索引命名不符合规范
**正确示例**：
```sql
CREATE INDEX idx_user_status ON user(status);
CREATE UNIQUE INDEX uniq_user_name ON user(name);
```
**错误示例**：
```sql
CREATE INDEX index1 ON user(status);  -- 命名无意义
CREATE INDEX idx_001 ON user(status);
```
**修复建议**：使用idx_/uniq_前缀命名

---

## MS-007: 字段注释
**规则**：字段必须有注释
**严重程度**：警告
**检查逻辑**：字段缺少COMMENT
**正确示例**：
```sql
CREATE TABLE user (
    id BIGINT COMMENT '用户ID',
    username VARCHAR(64) COMMENT '用户名',
    status TINYINT(1) DEFAULT 1 COMMENT '状态:0禁用,1启用'
);
```
**错误示例**：
```sql
CREATE TABLE user (
    id BIGINT,
    username VARCHAR(64),
    status TINYINT(1)
);
```
**修复建议**：所有字段添加COMMENT

---

## MS-008: 保留字冲突
**规则**：字段名避免使用数据库保留字
**严重程度**：警告
**检查逻辑**：字段名与MySQL保留字冲突
**正确示例**：
```sql
CREATE TABLE orders (
    id BIGINT,
    order_date DATE COMMENT '订单日期',
    order_desc VARCHAR(255) COMMENT '订单描述'
);
```
**错误示例**：
```sql
CREATE TABLE orders (
    id BIGINT,
    date DATE,      -- date是保留字
    `desc` VARCHAR(255)  -- desc是保留字，需转义
);
```
**修复建议**：使用非保留字或添加反引号转义

---

## MS-009: 禁止物理外键
**规则**：禁止使用物理外键，在应用层实现引用完整性
**严重程度**：严重
**检查逻辑**：表存在FOREIGN KEY约束
**正确示例**：
```sql
-- 不使用外键，在应用层保证引用完整性
CREATE TABLE order (
    id BIGINT,
    user_id BIGINT COMMENT '用户ID',
    INDEX idx_user_id (user_id)
);
```
**错误示例**：
```sql
CREATE TABLE order (
    id BIGINT,
    user_id BIGINT,
    FOREIGN KEY (user_id) REFERENCES user(id)
);
```
**修复建议**：移除物理外键，在应用层实现

---

## MQ-001: 禁止select *
**规则**：禁止使用select *，必须明确指定列名
**严重程度**：严重
**检查逻辑**：SQL包含SELECT *
**正确示例**：
```java
@Query("SELECT new User(u.id, u.name, u.email) FROM User u WHERE u.id = ?1")
User findById(Long id);
```
**错误示例**：
```java
@Query("SELECT * FROM user WHERE id = ?1")
User findById(Long id);
```
**修复建议**：明确列出需要的字段

---

## MQ-002: count使用
**规则**：使用count(*)统计数量
**严重程度**：警告
**检查逻辑**：使用count(列名)而非count(*)
**正确示例**：
```sql
SELECT COUNT(*) FROM user WHERE status = 1;
```
**错误示例**：
```sql
SELECT COUNT(id) FROM user WHERE status = 1;  -- 不准确，可能遗漏NULL
```
**修复建议**：使用COUNT(*)

---

## MQ-003: insert列名
**规则**：insert语句必须指定列名
**严重程度**：警告
**检查逻辑**：insert语句没有列名列表
**正确示例**：
```sql
INSERT INTO user(id, name, email) VALUES (1, 'test', 'test@example.com');
```
**错误示例**：
```sql
INSERT INTO user VALUES (1, 'test', 'test@example.com');  -- 无列名
```
**修复建议**：明确指定插入的列名

---

## MQ-004: SQL参数绑定
**规则**：禁止字符串拼接SQL，必须使用参数绑定
**严重程度**：严重
**检查逻辑**：使用+拼接SQL参数
**正确示例**：
```java
@Query("SELECT * FROM user WHERE name = ?1")
User findByName(String name);
```
**错误示例**：
```java
@Query("SELECT * FROM user WHERE name = '" + name + "'")  // SQL注入风险
```
**修复建议**：使用参数绑定

---

## MQ-005: 禁止truncate table
**规则**：禁止使用truncate table
**严重程度**：严重
**检查逻辑**：代码中存在truncate table
**正确示例**：
```java
// 使用DELETE FROM table
int rows = jdbcTemplate.update("DELETE FROM user WHERE status = 0");
```
**错误示例**：
```java
jdbcTemplate.execute("TRUNCATE TABLE user");  // 禁止使用
```
**修复建议**：使用DELETE或分批DELETE

---

## MQ-006: or条件优化
**规则**：or条件使用union all替代
**严重程度**：提示
**检查逻辑**：使用or连接可能走不到索引的条件
**正确示例**：
```sql
SELECT * FROM user WHERE id = 1
UNION ALL
SELECT * FROM user WHERE id = 2;
```
**错误示例**：
```sql
SELECT * FROM user WHERE id = 1 OR id = 2;  -- 可能无法利用索引
```
**修复建议**：使用UNION ALL替代OR

---

## MQ-007: 分页查询
**规则**：分页查询必须先count再分页
**严重程度**：警告
**检查逻辑**：直接分页无count
**正确示例**：
```java
int total = userRepository.countByStatus(1);
List<User> users = userRepository.findByStatus(1, page, size);
```
**错误示例**：
```java
// 直接LIMIT跳过大量数据
List<User> users = userRepository.findByStatus(1, page * size, size);
```
**修复建议**：先COUNT获取总数，计算总页数
