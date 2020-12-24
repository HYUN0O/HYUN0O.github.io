---
categories: 
   - study
tags:
   - Java
   - Kotlin
---







## 상속

부모 클래스/메소드/변수의 자원을 자식이 사용하는 행위를 '상속'이라고 한다.



Java에서는 `final` modifier을 통해 클래스/메소드/변수의 상속을 제한 할 수 있다.

하지만 Kotlin에서는 상속을 따로 허용하지 않으면 기본적으로 Java의 `final`과 같이 정의된다.

이에, Kotlin에서 함수를 상속 할 수 있도록 허용하기 위해서  `open` modifier를 사용한다.



## Classes

```java
final public class UninheritableClass { ... } // in Java
```

```kotlin
class UninheritableClass { ... } // in Kotlin
```

위 두 클래스는 모두 상속 **불가능**한 클래스이다.



```java
public class InheritableClass { ... } // in Java
```

```kotlin
open class InheritableClass { ... } // in Kotlin
```

위 두 클래스는 모두 상속 **가능**한 클래스이다.



```java
public class Parent { ... }
public class Child extends parent { ... } // in Java
```

```kotlin
open class Parent { ... }
class Child: Parent() { ... } // in Kotlin
```

위 두 코드는 동일하게, Child가 Parent라는 Class를 상속한다.



## Methods

```java
public final boolean UninheritableMethod() { ... } // in Java
```

```kotlin
fun UninheritableMethod(): Boolean { ... } // in Kotlin
```

두 메소드는 모두 상속 **불가능**하다.

## 

```java
public boolean InheritableMethod() { ... } // in Java
```

```kotlin
open fun InheritableMethod(): Boolean { ... } // in Kotlin
```

위 두 메소드는 모두 상속 **가능**한 메소드이다.



```java
public class Parent { 
	public boolean InheritableFun() { ... }
}
public class Child extends parent { 
    @Override
	public boolean InheritableFun() {
    	super.InheritableFun()
        ...
    }
} // in Java
```

```kotlin
open class Parent {
    open fun InheritableMethod(): Boolean { ... } 
}
class Child: Parent() { 
	override fun InheritableMethod(): Boolean { 
    	super.InheritableMethod()
    	...
    } 
} // in Kotlin
```

위 두 코드는 동일하게, Child가 Parent Class의 Method를 Override하여 사용한다.

Java에서는 `@Override` 어노테이션이 선택이지만, (물론 작성을 권장한다.)

Kotlin에서는 `override` modifier가 없으면 빌드 시 오류가 발생한다.



이례적으로, Kotlin에서도 `override`된 `fun`은 상속에 대해 열려있다.



## Variables

```java
public final int UninheritableVeriable = 0;
```

```kotlin
val UninheritableVeriable: Int = 0
```

두 변수는 모두 상속 **불가능**하다.

## 

```java
public int UninheritableVeriable = 0;
```

```kotlin
open val UninheritableVeriable: Int = 0
```

위 두 변수는 모두 상속 **가능**한 변수다.



## Examples

### Kotlin

```java
open class Parent {
    		fun fun1() { ... }
    open	fun fun2() { ... }
    override fun fun3() { ... }
}
```

```kotlin
class Child: Parent() {
    overrride fun fun1() { ... }	// cannot override
    overrride fun fun2() { ... }	// overridable
    overrride fun fun3() { ... }	// overridable
}
```



fun1은 Java의 `Final`과 동일하기 때문에, override 할 수 없다.





## 참고 사이트

  - https://androidtest.tistory.com/102
  - https://zerogdev.blogspot.com/2019/06/kotlin-open-final-abstract.html