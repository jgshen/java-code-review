# 日期时间规则

## DT-001: SimpleDateFormat线程安全
**规则**：SimpleDateFormat不是线程安全的，禁止定义为static共享
**检查逻辑**：static字段类型为SimpleDateFormat
**正确示例**：
```java
public class DateUtils {
    private static final String PATTERN = "yyyy-MM-dd HH:mm:ss";

    public static String format(Date date) {
        SimpleDateFormat sdf = new SimpleDateFormat(PATTERN);
        return sdf.format(date);
    }

    // 或使用ThreadLocal
    private static final ThreadLocal<SimpleDateFormat> sdfHolder =
        ThreadLocal.withInitial(() -> new SimpleDateFormat(PATTERN));
}
```
**错误示例**：
```java
public class DateUtils {
    private static final SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd");
    // 多个线程共享同一sdf，导致线程不安全
}
```
**修复建议**：使用局部变量或ThreadLocal

---

## DT-002: Date与Calendar
**规则**：Date与CalendarAPI同时存在时，优先使用LocalDateTime
**检查逻辑**：同时使用Date和LocalDateTime
**正确示例**：
```java
LocalDateTime now = LocalDateTime.now();
LocalDateTime tomorrow = now.plusDays(1);
String formatted = now.format(DateTimeFormatter.ISO_LOCAL_DATE_TIME);
```
**错误示例**：
```java
Date date = new Date();
Calendar calendar = Calendar.getInstance();
calendar.setTime(date);
```
**修复建议**：使用java.time包下的类

---

## DT-003: YearMonth-DayOfWeek
**规则**：Year、Month、Week不建议使用枚举，推荐使用YearMonth类
**检查逻辑**：检查是否使用了不推荐的枚举
**正确示例**：
```java
YearMonth currentMonth = YearMonth.now();
int daysInMonth = currentMonth.lengthOfMonth();
```
**错误示例**：
```java
// 不推荐使用Calendar.Month等枚举
int month = Calendar.getInstance().get(Calendar.MONTH);
```
**修复建议**：使用YearMonth替代

---

## DT-004: 日期时间比较
**规则**：日期时间比较使用equals方法，不使用compareTo
**检查逻辑**：日期比较使用compareTo
**正确示例**：
```java
if (date1.equals(date2)) {
    // 相同
}
if (date1.isBefore(date2)) {
    // 早于
}
if (date1.isAfter(date2)) {
    // 晚于
}
```
**错误示例**：
```java
if (date1.compareTo(date2) == 0) {
    // 相同
}
```
**修复建议**：使用equals/isBefore/isAfter方法

---

## DT-005: 日期字符串格式
**规则**：日期格式字符串必须为常量
**检查逻辑**：日期格式作为字面量而非常量
**正确示例**：
```java
private static final String DATE_TIME_PATTERN = "yyyy-MM-dd HH:mm:ss";
private static final String DATE_PATTERN = "yyyy-MM-dd";

public String format(Date date) {
    return new SimpleDateFormat(DATE_PATTERN).format(date);
}
```
**错误示例**：
```java
public String format(Date date) {
    return new SimpleDateFormat("yyyy-MM-dd").format(date);  // 魔法值
}
```
**修复建议**：将日期格式定义为常量
