# Java代码审查

> *"好的代码本身就是最好的文档。"* — Martin Fowler

**让代码审查成为一种习惯，让规范成为自然。**

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Java](https://img.shields.io/badge/Java-17+-blue.svg)](https://java.com)
[![Claude Code](https://img.shields.io/badge/Claude%20Code-Skill-blueviolet)](https://claude.ai/code)
[![阿里Java开发手册](https://img.shields.io/badge/阿里开发手册-泰山版-green.svg)](https://github.com/alibaba/Alibaba-Java-Coding-Guidelines)

&nbsp;

基于阿里Java开发手册（泰山版）对Java代码进行全方位规范审查  
覆盖**编程规约**、**MySQL规约**、**工程规约**、**安全规约**四大类  
输出结构化的违规报告，包含规则描述、违规位置和修复建议

⚠️ **本工具仅辅助审查，最终代码质量由开发者负责。**

---

## 安装

### Claude Code

> **重要**：Claude Code 从 **git 仓库根目录** 的 `.claude/skills/` 查找 skill。请在正确的位置执行。

```bash
# 安装到当前项目（在 git 仓库根目录执行）
mkdir -p .claude/skills
git clone https://github.com/你的用户名/java-code-review .claude/skills/java-code-review

# 或安装到全局（所有项目都能用）
git clone https://github.com/你的用户名/java-code-review ~/.claude/skills/java-code-review
```

---

## 使用

在 Claude Code 中输入：

```
帮我审查一下src目录下的Java代码
```

或

```
检查这段代码是否符合阿里Java开发手册规范
```

### 触发方式

| 触发词 | 说明 |
|--------|------|
| `审查代码` | 审查指定文件或目录 |
| `检查代码规范` | 检查代码规范性 |
| `代码review` | 进行代码审查 |
| `代码检查` | 检查代码 |
| `规范审查` | 规范审查 |

---

## 效果示例

> 输入：`帮我审查一下src目录下的Java代码`

**输出：**

```
## Java代码审查报告

**审查时间**：2026-04-02
**审查范围**：src/main/java/com/example/
**审查依据**：阿里Java开发手册（泰山版）

---

### 审查概要

| 指标 | 数值 |
|------|------|
| 审查文件数 | 12 |
| 违规规则数 | 10 |
| 违规总数 | 18 |
| 严重违规 | 5 |
| 警告级别 | 10 |
| 提示级别 | 3 |

---

### 违规详情

#### [严重] OP-003 Object.equals容易产生NPE
**规则**：Object的equals方法容易产生NPE，应使用Objects.equals()
**文件**：UserService.java:42
```java
if (str.equals("expected")) {  // str可能为null
```
**修复建议**：
```java
if (Objects.equals(str, "expected")) { }
```

---

#### [严重] SE-003 SQL参数绑定
**规则**：禁止字符串拼接SQL，必须使用参数绑定
**文件**：UserDAO.java:28
```java
List<User> users = jdbc.query("SELECT * FROM user WHERE name = '" + name + "'");
```
**修复建议**：
```java
List<User> users = jdbc.query("SELECT * FROM user WHERE name = ?", name);
```

---

#### [严重] MS-004 价格字段使用DOUBLE
**规则**：用于表示价格必须使用DECIMAL，禁止使用DOUBLE
**文件**：Product.java:15
```java
private double price;  // DOUBLE精度问题
```
**修复建议**：
```java
private BigDecimal price;  // 使用BigDecimal
```

---

#### [警告] NC-004 Boolean变量命名
**规则**：Boolean变量不应使用is/has/can/will前缀
**文件**：User.java:20
```java
private boolean isActive;
```
**修复建议**：
```java
private boolean active;
```

---

#### [警告] EH-005 finally必须释放资源
**规则**：finally中必须释放资源，使用try-with-resources更佳
**文件**：DataProcessor.java:55
```java
Connection conn = dataSource.getConnection();
// operation
// 未关闭连接
```
**修复建议**：
```java
try (Connection conn = dataSource.getConnection()) {
    // operation
}
```

---

### 规则覆盖统计

| 规则类别 | 检查规则数 | 违规数 |
|----------|-----------|--------|
| 命名风格 (NC) | 10 | 1 |
| 常量定义 (CT) | 7 | 0 |
| 代码格式 (CF) | 9 | 0 |
| OOP规约 (OP) | 11 | 2 |
| 日期时间 (DT) | 5 | 0 |
| 集合处理 (CL) | 10 | 0 |
| 并发处理 (CC) | 8 | 0 |
| 控制语句 (CS) | 9 | 0 |
| 注释规约 (CM) | 8 | 0 |
| 异常处理 (EH) | 8 | 1 |
| 日志规约 (LG) | 9 | 0 |
| MySQL建表 (MS) | 9 | 1 |
| MySQL查询 (MQ) | 7 | 1 |
| 工程规约 (EG) | 10 | 0 |
| 安全规约 (SE) | 12 | 2 |

---

## 规则体系

本项目基于阿里Java开发手册（泰山版），规则ID编码如下：

| 前缀 | 类别 | 说明 |
|------|------|------|
| NC | Naming Conventions | 命名风格 |
| CT | Constants | 常量定义 |
| CF | Code Format | 代码格式 |
| OP | OOP Rules | OOP规约 |
| DT | Date Time | 日期时间 |
| CL | Collections | 集合处理 |
| CC | Concurrency | 并发处理 |
| CS | Control Statements | 控制语句 |
| CM | Comments | 注释规约 |
| EH | Exception Handling | 异常处理 |
| LG | Logging | 日志规约 |
| MS | MySQL ORM | MySQL建表 |
| MQ | MySQL Query | MySQL查询 |
| EG | Engineering | 工程规约 |
| SE | Security | 安全规约 |

---

## 项目结构

本项目遵循 [AgentSkills](https://agentskills.io) 开放标准：

```
java-code-review/
├── SKILL.md                      # Skill 入口
├── README.md                     # 项目文档
└── references/                   # 渐进式披露规则文档
    ├── 01-naming-conventions.md  #   命名风格规则 (10条)
    ├── 02-constants.md          #   常量定义规则 (7条)
    ├── 03-code-format.md        #   代码格式规则 (9条)
    ├── 04-oop-rules.md          #   OOP规约 (11条)
    ├── 05-date-time.md          #   日期时间规则 (5条)
    ├── 06-collections.md        #   集合处理规则 (10条)
    ├── 07-concurrency.md        #   并发处理规则 (8条)
    ├── 08-control-statements.md #   控制语句规则 (9条)
    ├── 09-comments.md           #   注释规约 (8条)
    ├── 10-exception-handling.md #   异常处理规则 (8条)
    ├── 11-logging.md            #   日志规约 (9条)
    ├── 12-mysql-orms.md          #   MySQL规约 (17条)
    ├── 13-engineering.md        #   工程规约 (10条)
    └── 14-security.md           #   安全规约 (12条)
```

**规则总计**：130+条规则

---

## 严重程度定义

| 级别 | 说明 | 处理建议 |
|------|------|----------|
| **严重** | 影响系统正确性、安全性或性能的违规 | 必须修复 |
| **警告** | 影响可维护性或可能导致问题的代码 | 建议修复 |
| **提示** | 代码风格建议 | 可选修复 |

---

## 规则亮点

### 编程规约

| 规则 | 说明 |
|------|------|
| NC-001 | 抽象类必须以Abstract/Base开头 |
| NC-002 | 枚举类必须以Enum结尾 |
| NC-003 | 常量必须全大写加下划线 |
| NC-004 | Boolean变量不使用is/has前缀 |
| NC-006 | 接口类名不加I前缀 |
| NC-010 | POJO属性使用包装类型，不使用基本类型 |
| CT-001 | 禁止魔法值 |
| CT-002 | Long类型后缀必须大写L |
| CT-006 | if/for/while必须使用大括号 |
| OP-003 | Object.equals使用Objects.equals()避免NPE |
| OP-004 | POJO类属性使用BigDecimal而非float/double |
| OP-005 | 构造方法禁止包含业务逻辑 |
| OP-006 | 工具类不允许实例化 |
| OP-008 | equals覆写时必须同时覆写hashCode |
| EH-001 | 禁止捕获RuntimeException |
| EH-005 | finally必须释放资源 |
| LG-003 | 敏感信息禁止打印到日志 |

### MySQL规约

| 规则 | 说明 |
|------|------|
| MS-002 | 布尔字段不得使用is_xxx命名 |
| MS-003 | 字段必须NOT NULL并提供默认值 |
| MS-004 | 价格使用DECIMAL而非DOUBLE |
| MS-005 | 使用自增主键而非业务复合主键 |
| MS-009 | 禁止使用物理外键 |
| MQ-001 | 禁止select * |
| MQ-004 | 必须使用SQL参数绑定 |
| MQ-005 | 禁止使用truncate table |

### 安全规约

| 规则 | 说明 |
|------|------|
| SE-001 | 密码必须加密存储 |
| SE-002 | 敏感数据不打印日志 |
| SE-003 | SQL必须参数绑定 |
| SE-004 | 文件上传必须验证 |
| SE-005 | 必须进行XSS防护 |
| SE-006 | 必须开启CSRF防护 |
| SE-010 | 禁止硬编码密钥 |

---

## 注意事项

* **审查范围**：可指定文件或目录，未指定时自动搜索Java文件（排除target/、build/）
* **规则覆盖**：本工具覆盖阿里开发手册的主要规则，部分细节规则需人工复查
* **误报可能**：部分规则存在边界情况，审查结果仅供参考
* **建议结合**：IDE的实时检查 + 本工具的批量审查 + 代码评审

---

## 参考资料

* [阿里Java开发手册（泰山版）](https://github.com/alibaba/Alibaba-Java-Coding-Guidelines)
* [Alibaba Java Coding Guidelines](https://plugins.jetbrains.com/plugin/10046-alibaba-java-coding-guidelines)
