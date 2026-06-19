# 자바 if문과 equals() — 값 비교 vs 참조 비교

자바에서 조건문으로 값을 비교할 때, 문자열은 `equals()`로 비교해야 한다는 점을 정리했습니다.

## equals() — 값(내용) 비교

```java
if (num.equals(num2)) { }
```
`num`과 `num2`를 비교해 둘이 같으면 `true`, 다르면 `false`를 반환합니다.

```java
String num = "아버지";
String num2 = "아버지";
num.equals(num2);   // true
```

```java
String name1 = "아버지";
String name2 = "어머니";
name1.equals(name2);   // false
```

> **`equals()`는 값(내용)을 비교합니다.** `Object` 클래스에서 상속된 메서드지만, 보통 `String`, `Integer`, `List` 등에서 **재정의(override)** 되어 있습니다.

## == — 숫자 비교

숫자를 비교할 때는 `==`를 사용합니다.

```java
if (a == b) { }
```
