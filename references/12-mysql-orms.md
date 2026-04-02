# MySQL规约

## MS-001: 建表命名
**规则**：表名和字段名使用小写字母，单词间用下划线分隔
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
**检查逻辑**：字段名为is_xxx且类型为tinyint/boolean
**正确示例**：
```sql
CREATE TABLE user (
    id BIGINT,
    active TINYINT(1),        -- 使用active
    deleted TINYINT(1)       -- 使用deleted
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

## MS-003: 主键命名
**规则**：主键命名为pk_xxx
**检查逻辑**：主键字段不以pk_为前缀
**正确示例**：
```sql
CREATE TABLE user (
    pk_user_id BIGINT PRIMARY KEY
);
```
**错误示例**：
```sql
CREATE TABLE user (
    user_id BIGINT PRIMARY KEY
);
```
**修复建议**：将主键命名为pk_xxx

---

## MS-004: 保留字冲突
**规则**：字段名避免使用数据库保留字
**检查逻辑**：字段名与MySQL保留字冲突
**正确示例**：
```sql
CREATE TABLE orders (
    id BIGINT,
    order_date DATE,
    order_desc VARCHAR(255)
);
```
**错误示例**：
```sql
CREATE TABLE orders (
    id BIGINT,
    date DATE,      -- date是保留字
    desc VARCHAR(255)  -- desc是保留字
);
```
**修复建议**：使用非保留字或添加反引号转义

---

## MS-005: 字段注释
**规则**：字段必须有注释
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

## MS-006: 索引命名
**规则**：索引命名为idx_xxx、uniq_xxx
**检查逻辑**：索引命名不符合规范
**正确示例**：
```sql
CREATE INDEX idx_user_status ON user(status);
CREATE UNIQUE INDEX uniq_user_name ON user(name);
```
**错误示例**：
```sql
CREATE INDEX index1 ON user(status);  -- 命名无意义
```
**修复建议**：使用idx_/uniq_前缀命名

---

## MQ-001: 禁止select *
**规则**：禁止使用select *，必须明确指定列名
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

## MQ-004: 分页查询
**规则**：分页查询必须先count再分页
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

---

## MQ-005: 批量操作
**规则**：批量操作使用executeBatch
**检查逻辑**：循环中逐条执行SQL
**正确示例**：
```java
jdbcTemplate.batchUpdate("INSERT INTO user(name) VALUES (?)",
    users.stream().map(u -> new Object[]{u.getName()}).toList());
```
**错误示例**：
```java
for (User user : users) {
    jdbcTemplate.update("INSERT INTO user(name) VALUES (?)", user.getName());
}
```
**修复建议**：使用批量操作接口

---

## MQ-006: SQL注入防护
**规则**：禁止字符串拼接SQL，必须使用参数绑定
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

## MQ-007: or条件优化
**规则**：or条件使用union all替代
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
