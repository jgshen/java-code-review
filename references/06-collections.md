# 集合处理规则

## CL-001: HashMap clone
**规则**：方法入参是HashMap时需clone后再操作
**严重程度**：警告
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
**严重程度**：警告
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
**严重程度**：警告
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
**严重程度**：严重
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

## CL-005: Map的hashCode和equals
**规则**：Map的key的hashCode和equals方法必须一致
**严重程度**：严重
**检查逻辑**：自定义类作为Map key但未正确覆写hashCode和equals
**正确示例**：
```java
public class Key {
    private String name;

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;
        Key key = (Key) o;
        return Objects.equals(name, key.name);
    }

    @Override
    public int hashCode() {
        return Objects.hash(name);
    }
}
```
**错误示例**：
```java
public class Key {
    private String name;
    // 只覆写了equals未覆写hashCode，或反之
}
```
**修复建议**：同时覆写hashCode和equals

---

## CL-006: 集合判空
**规则**：使用isEmpty()判断集合为空，不使用size()==0
**严重程度**：提示
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
**严重程度**：提示
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

---

## CL-008: 批量操作
**规则**：批量添加使用addAll，批量删除使用removeAll
**严重程度**：提示
**检查逻辑**：循环中添加删除元素
**正确示例**：
```java
list.addAll(otherList);
list.removeAll(toRemove);
```
**错误示例**：
```java
for (Item item : toAdd) {
    list.add(item);  // 逐个添加
}
```
**修复建议**：使用批量操作方法

---

## CL-009: Arrays.asList
**规则**：Arrays.asList返回的是定长List，不能进行增删操作
**严重程度**：警告
**检查逻辑**：对Arrays.asList结果进行增删操作
**正确示例**：
```java
List<String> list = new ArrayList<>(Arrays.asList("a", "b", "c"));
list.add("d");
```
**错误示例**：
```java
List<String> list = Arrays.asList("a", "b", "c");
list.add("d");  // UnsupportedOperationException
```
**修复建议**：如果需要增删，用new ArrayList包装

---

## CL-010: Set和Map初始化
**规则**：Set和Map初始化时指定容量，避免扩容
**严重程度**：提示
**检查逻辑**：大HashSet/HashMap未指定容量
**正确示例**：
```java
Set<String> set = new HashSet<>(expectedSize);
Map<String, Object> map = new HashMap<>(expectedSize);
```
**错误示例**：
```java
Set<String> set = new HashSet<>();  // 默认容量16，可能多次扩容
Map<String, Object> map = new HashMap<>();
```
**修复建议**：预估容量并使用HashSet(int initialCapacity)等
