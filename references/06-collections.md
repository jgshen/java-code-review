# 集合处理规则

## CL-001: HashMap clone
**规则**：方法入参是HashMap时需clone后再操作
**检查逻辑**：方法参数为HashMap且直接修改
**正确示例**：
```java
public void process(Map<String, Object> data) {
    Map<String, Object> copy = new HashMap<>(data);  // clone
    copy.put("key", "value");  // 操作副本
}
```
**错误示例**：
```java
public void process(Map<String, Object> data) {
    data.put("key", "value");  // 直接修改入参
}
```
**修复建议**：clone后再操作，或返回新Map

---

## CL-002: ArrayList subList
**规则**：ArrayList的subList不可强制类型转换为其实现类
**检查逻辑**：将subList强转为ArrayList
**正确示例**：
```java
List<String> sub = list.subList(0, 10);
List<String> newList = new ArrayList<>(sub);
```
**错误示例**：
```java
ArrayList<String> sub = (ArrayList<String>) list.subList(0, 10);
```
**修复建议**：subList返回的是SubList而非ArrayList

---

## CL-003: 集合toArray
**规则**：集合转数组必须指定类型参数
**检查逻辑**：使用无参toArray()
**正确示例**：
```java
List<String> list = Arrays.asList("a", "b", "c");
String[] array = list.toArray(new String[0]);
```
**错误示例**：
```java
String[] array = list.toArray();  // 返回Object[]
```
**修复建议**：使用`toArray(new String[0])`或`toArray(new String[list.size()])`

---

## CL-004: ConcurrentHashMap
**规则**：ConcurrentHashMap不允许null值，不可用null判断
**检查逻辑**：ConcurrentHashMap.containsValue(null)
**正确示例**：
```java
ConcurrentHashMap<String, String> map = new ConcurrentHashMap<>();
if (map.containsKey("key")) {
    String value = map.get("key");
}
```
**错误示例**：
```java
if (map.get("key") == null) {  // 无法区分key不存在和value为null
    // ...
}
```
**修复建议**：使用containsKey判断

---

## CL-005: 集合类型选择
**规则**：按需选择集合类型，避免HashMap/HashSet代替List
**检查逻辑**：HashMap/HashSet作为List使用
**正确示例**：
```java
List<User> userList = new ArrayList<>();
Set<String> nameSet = new HashSet<>();
```
**错误示例**：
```java
Map<String, User> users = new HashMap<>();  // 当需要列表时使用Map
users.put("1", user1);
users.put("2", user2);
users.values();  // 作为List使用
```
**修复建议**：根据语义选择合适的集合类型

---

## CL-006: 集合判空
**规则**：使用isEmpty()判断集合为空，不使用size()==0
**检查逻辑**：使用size()==0或size()!=0
**正确示例**：
```java
if (list.isEmpty()) { }
if (!collection.isEmpty()) { }
```
**错误示例**：
```java
if (list.size() == 0) { }
if (list.size() != 0) { }
```
**修复建议**：使用isEmpty()方法

---

## CL-007: foreach遍历
**规则**：遍历集合时使用增强for循环或迭代器，不使用随机访问遍历List
**检查逻辑**：for循环遍历List使用get(i)
**正确示例**：
```java
for (String item : list) {
    System.out.println(item);
}
```
**错误示例**：
```java
for (int i = 0; i < list.size(); i++) {
    System.out.println(list.get(i));  // ArrayList可用get，但低效
}
```
**修复建议**：使用增强for循环或迭代器
