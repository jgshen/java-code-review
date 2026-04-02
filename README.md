# Java代码审查

> *"好的代码本身就是最好的文档。"* — Martin Fowler

**让代码审查成为一种习惯，让规范成为自然。**

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Java](https://img.shields.io/badge/Java-17+-blue.svg)](https://java.com)
[![Claude Code](https://img.shields.io/badge/Claude%20Code-Skill-blueviolet)](https://claude.ai/code)
[![阿里Java开发手册](https://img.shields.io/badge/阿里开发手册-泰山版-green.svg)](https://github.com/alibaba/Alibaba-Java-Coding-Guidelines)

&nbsp;

基于阿里Java开发手册（泰山版）对Java代码进行全方位规范审查  
覆盖**编程规约**、**MySQL规约**、**工程规约**三大类  
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
| 违规规则数 | 8 |
| 违规总数 | 15 |
| 严重违规 | 3 |
| 警告级别 | 7 |
| 提示级别 | 5 |

---

### 违规详情

#### [严重] NC-001 抽象类命名错误
**规则**：抽象类命名必须以Abstract或Base开头
**文件**：UserService.java:15
```java
public abstract class UserService {
```
**修复建议**：
```java
public abstract class AbstractUserService {
```

---

#### [警告] EH-001 捕获RuntimeException
**规则**：不可捕获RuntimeException、Error及其子类
**文件**：DataProcessor.java:42
```java
try {
    processData();
} catch (RuntimeException e) {
    logger.error("Error", e);
}
```
**修复建议**：
```java
try {
    processData();
} catch (IOException e) {
    throw new BusinessException("Data processing failed", e);
}
```

---

#### [严重] MQ-001 使用select *
**规则**：禁止使用select *，必须明确指定列名
**文件**：UserDAO.java:28
```java
List<User> users = jdbc.query("SELECT * FROM users", status);
```
**修复建议**：
```java
List<User> users = jdbc.query(
    "SELECT id, name, email, status FROM users WHERE status = ?",
    status
);
```

---

### 规则覆盖统计

| 规则类别 | 检查规则数 | 违规数 |
|----------|-----------|--------|
| 命名风格 (NC) | 10 | 2 |
| 常量定义 (CT) | 5 | 1 |
| 代码格式 (CF) | 7 | 0 |
| OOP规约 (OP) | 7 | 1 |
| 日期时间 (DT) | 5 | 0 |
| 集合处理 (CL) | 7 | 1 |
| 并发处理 (CC) | 7 | 0 |
| 控制语句 (CS) | 8 | 0 |
| 注释规约 (CM) | 7 | 0 |
| 异常处理 (EH) | 8 | 2 |
| 日志规约 (LG) | 8 | 1 |
| MySQL建表 (MS) | 6 | 0 |
| MySQL查询 (MQ) | 7 | 1 |

---

## 规则体系

本项目基于阿里Java开发手册（泰山版），规则ID编码如下：

| 前缀 | 类别 | 示例 |
|------|------|------|
| NC | Naming Conventions（命名风格） | NC-001 抽象类命名 |
| CT | Constants（常量定义） | CT-001 禁止魔法值 |
| CF | Code Format（代码格式） | CF-001 缩进风格 |
| OP | OOP Rules（OOP规约） | OP-001 静态方法调用 |
| DT | Date Time（日期时间） | DT-001 SimpleDateFormat线程安全 |
| CL | Collections（集合处理） | CL-001 HashMap clone |
| CC | Concurrency（并发处理） | CC-001 线程池命名 |
| CS | Control Statements（控制语句） | CS-001 switch完整性 |
| CM | Comments（注释规约） | CM-001 类注释 |
| EH | Exception Handling（异常处理） | EH-001 禁止捕获RuntimeException |
| LG | Logging（日志规约） | LG-001 日志对象定义 |
| MS | MySQL ORM（MySQL建表） | MS-002 布尔字段命名 |
| MQ | MySQL Query（MySQL查询） | MQ-001 禁止select * |
| EG | Engineering（工程规约） | EG-001 应用分层 |

---

## 项目结构

本项目遵循 [AgentSkills](https://agentskills.io) 开放标准：

```
java-code-review/
├── SKILL.md                     # Skill 入口
├── references/                  # 渐进式披露规则文档
│   ├── 01-naming-conventions.md  #   命名风格规则
│   ├── 02-constants.md          #   常量定义规则
│   ├── 03-code-format.md        #   代码格式规则
│   ├── 04-oop-rules.md         #   OOP规约
│   ├── 05-date-time.md         #   日期时间规则
│   ├── 06-collections.md       #   集合处理规则
│   ├── 07-concurrency.md       #   并发处理规则
│   ├── 08-control-statements.md#   控制语句规则
│   ├── 09-comments.md          #   注释规约
│   ├── 10-exception-handling.md#   异常处理规则
│   ├── 11-logging.md           #   日志规约
│   ├── 12-mysql-orms.md        #   MySQL规约
│   └── 13-engineering.md       #   工程规约
└── README.md
```

---

## 规则亮点

### 编程规约

* **命名风格**：抽象类必须以Abstract/Base开头，枚举类必须以Enum结尾
* **常量定义**：禁止魔法值，Long类型后缀必须大写L
* **OOP规约**：静态方法通过类名调用，多线程使用ThreadLocal封闭数据
* **异常处理**：禁止捕获RuntimeException，finally中不执行return
* **日志规约**：日志对象必须private static final，禁止字符串拼接

### MySQL规约

* **建表**：布尔字段不得使用is_xxx命名，主键命名为pk_xxx
* **SQL查询**：禁止select *，使用count(*)统计数量

### 工程规约

* **应用分层**：DO/DTO/VO/BO分层清晰
* **二方库依赖**：groupId:artifactId:version格式

---

## 严重程度定义

| 级别 | 说明 |
|------|------|
| **严重** | 影响系统正确性或安全性的违规，必须修复 |
| **警告** | 影响可维护性或性能的问题，建议修复 |
| **提示** | 代码风格建议，可选修复 |

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
