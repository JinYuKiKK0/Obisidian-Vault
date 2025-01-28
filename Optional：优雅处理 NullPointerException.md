### Optional的核心思想
- Optional是一个容器，可以包含非空值也可以为空.
- 它强制开发者显式地处理值为null的情况，而不是隐式地忽略null.
### 创建Optional对象
- **`Optional.of(value)`**: 创建一个包含非空值的 `Optional`。如果 `value` 为 `null`，会抛出 `NullPointerException`。
```java
Optional<String> optional = Optional.of("Hello");
```
- **`Optional.ofNullable(value)`**: 创建一个 `Optional`，如果 `value` 为 `null`，则返回一个空的 `Optional`。
```java
Optional<String> optional = Optional.ofNullable(null);
```
- `Optional.empty()`:创建一个空的Optional
```java
Optional<String> optional = Optional.empty();
```
###  检查Optional是否有值
- `isPresent()`:检查Optional是否包含非空值
```java
if (optional.isPresent()) {
    System.out.println("Value is present: " + optional.get());
}
```
- `isEmpty()`:检查Optional是否为空
```java
if (optional.isEmpty()) {
    System.out.println("Value is absent");
}
```
### 获取Optional中的值
- `get()`: 获取 `Optional` 中的值。如果 `Optional` 为空，会抛出 `NoSuchElementException`
```java
String value = optional.get(); // 慎用，可能导致异常
```
- **`orElse(defaultValue)`**: 获取 `Optional` 中的值，如果为空则返回默认值。
```java
String value = optional.orElse("Default Value");
```
- **`orElseGet(supplier)`**: 获取 `Optional` 中的值，如果为空则通过 `Supplier` 函数生成默认值
```java
String value = optional.orElseGet(() -> "Generated Default Value");
```
- **`orElseThrow(exceptionSupplier)`**: 获取 `Optional` 中的值，如果为空则抛出指定的异常。
```java
String value = optional.orElseThrow(() -> new RuntimeException("Value not found"));
```