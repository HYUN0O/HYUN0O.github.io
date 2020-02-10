---
categories: 
   - Study
tags:
   - kotlin
---
오늘은 Kotlin (2)에서 다룬 when문과 성격은 비슷하지만 갈래는 다른 Null Operator와, 예외처리에 관해 공부하는 시간을 갖겠다.

이후에 나오는 코드들은 [Kotlin Playground](https://play.kotlinlang.org/)에서 직접 입력하고, 실행하며 학습하면 도움이 된다!

바로 본론으로 넘어가겠다.

# Kotlin
## ☝ Null Operators

### 1.1 Nullable Operator (?)

**Kotlin**
```kotlin
fun upperStr(str: String) {
    println(str.toUpperCase())
}

fun main() {
    upperStr("hellllo")
}
```

**Result**
```
HELLLLO
```

일반적으로 위처럼 `fun`을 정의하고, `fun`에 올바른 생성자를 입력하면, 하지만, 어떤 문제로 인해 함수에 `null`이 입력된다면 오류가 발생한다. 아래 예시를 보자.

**Kotlin**
```kotlin
fun upperStr(str: String) {
    println(str.toUpperCase())
}

fun main() {
    upperStr(null)
}
```

**Result**
```
ERROR : Null can not be a value of a non-null type String
```

이런식으로 기본적으로 `null`은 함수의 인자로 사용할 수 없다.

이를 가능하게 해주는 것이 Nullable Operator이다.

아래처럼 코딩하면 `null`을 인자로 사용할 수 있다.

물론 이 경우는 `toUpperCase()`가 `null`을 지원하지 않으므로, if문으로 예외처리를 진행하였다.

**Kotlin**
```kotlin
fun upperStr(str: String?) {
    if (str != null){
        println(str.toUpperCase())
    } else {
        println(null)
    }
}

fun main() {
    upperStr(null)
    upperStr("helllllo")
}
```

**Result**

```
null
HELLLLLO
```

모든 변수형에 `?`을 붙이는 것으로, nullable하게 만들 수 있다.

### 1.2 Null Safe Operator (?.)

Null Safe Operator은, 왼쪽의 변수가 `null`이 아니면 오른쪽 함수를 수행하고, `null`이면 `null`을 반환시킨다.

말에 null이 3번이나 들어가서 무슨 말인지 헷갈릴 수 있으니, 예시를 들어 직접 확인하자.

**Kotlin**
```kotlin
fun upperStr(str: String?) {
    println(str?.toUpperCase())
}

fun main() {
    upperStr(null)
    upperStr("helllllo")
}
```

**Result**
```
null
HELLLLLO
```

1.1의 예제와 같은 함수지만, 굉장히 짧게 구현 가능해졌다.

즉, `variable?.function()`는 `if (variable != null) function() else null`과 **동치관계**다.

### 1.3 Elvis Operator (?:)

왼쪽의 변수가 `null`일 때 `null`을 return하는 연산자가 있다면, `null`외의 커스텀 한 값을 return하는 연산자도 있다.

?: 의 모양이 엘비스 프레슬리의 헤어스타일을 닮아서 Elvis Operator이라고 부른다고 한다.

**Kotlin**
```kotlin
fun upperStr(str: String?) {
	var result = (str ?: "null is not string").toUpperCase()
    println(result)
}

fun main() {
    upperStr(null)
    upperStr("helllllo")
}
```

**Result**
```
NULL IS NOT STRING
HELLLLLO
```

형식은 조금 다르지만, 마찬가지로 null일 때 다른 값으로 catch하는 연산자이다.

마찬가지로, `variable = target?:safety`는 `variable = if (target != null) target else safety`와 **동치관계**다.

### 1.4 let 구문

`not null`일 때 특정 인자를 전달해주는 `?:`도 있지만, 특정 구문을 실행해주는 함수도 존재하는데, 바로 `let`이다.

`let` 함수 안에서는 자기 자신을 `it`으로 lambda 식 내부로 전달해준다.

아래 예제를 살펴보면서 이해해보자.

**Kotlin**
```kotlin
fun upperStr(str: String?) {
    str?.let{
        println(it.toUpperCase())
        return
    }
    println("your string is null")
}

fun main() {
    upperStr(null)
    upperStr("helllllo")
}
```

**Result**
```
your string is null
HELLLLLO
```

main 함수에서 첫 번째로 `null`을 인자로 `upperStr`을 호출했을 때는 `let` 내부의 구문이 실행되지 않았다.

두 번째로 not null한 인자로 `upperStr`을 호출했을 때는 `let`내부의 구문에서 `str`을 `it`으로 호출하였고, 정상적으로 실행되어 대문자화 된 String을 출력하고, 함수를 return하였다.


### 1.5 nullable type - extension function

nullable type으로 선언한 변수가 `null`일 때, 간단히 catch 할 수 있는 확장 함수도 존재한다.

대표적으로 `isNullOrBlank()`가 있는데, 이 함수를 사용하면 변수가 `null`이거나 `""`일 경우, `true`를 반환한다.

아래 예시를 참고하여 `null`일 때와 `blank`일 때의 예외를 catch하자.

**Kotlin**
```kotlin
fun upperStr(str: String?) {
    if (str.isNullOrBlank()) {
        var result = if(str=="") "blank" else "null"
        println("your string is $result")
    } else {
        println(str.toUpperCase())
    }
}

fun main() {
    upperStr(null)
    upperStr("")
    upperStr("helllllo")
}
```

**Result**
```
your string is null
your string is blank
HELLLLLO
```

### 1.6 Not Null Operator (!!)

코딩을 하다보면, nullable로 선언을 하였음에도 어떤 부분에서는 절대 null이 들어가지 않는 부분이 생길 수 있다.

하지만 컴파일러는 null이 들어오지 않는다는 점을 모르기 때문에, 계속해서 null에 대한 처리를 진행한다.

물론 null이 아니라는 뜻을 갖는 연산자이기 때문에, 값에 null이 들어가면 NullPointerException 오류가 발생한다.

**Kotlin**
```kotlin
fun upperStr(str: String?) {
    println(str!!.toUpperCase())
}

fun main() {
    upperStr("helllllo")
}
```

**Result**
```
HELLLLLO
```

 만약 null을 넣는다면...

**Kotlin**
```kotlin
fun upperStr(str: String?) {
    println(str!!.toUpperCase())
}

fun main() {
    upperStr(null)
}
```

**Result**
```
Exception in thread "main" kotlin.KotlinNullPointerException
	at MainKt.upperStr(Main.kt:2)
	at MainKt.main(Main.kt:6)
	at MainKt.main(Main.kt)
```

이렇게 NullPointerException이 발생한다.

Exception에 대해서는 바로 이어서 다뤄보자.

## 🤞 예외처리

Exception은 크게 두 가지 이유로 발생한다.

1. 사용자의 잘못된 조작
2. 개발자의 잘못된 코딩

Exception은 정말 많고, 상황에 따라 여러가지 예외처리 방법이 존재 하지만, 오늘은 예외처리 사용 방법에 대해 간단히 다루려고 한다.

그 중 런타임 에러를 발생시키는 `RuntimeException`을 상속하는 `Exception`의 대표적인 예는 `NullPointerException`, `ArrayIndexOutOfBounds` 등이 있다.

이들의 공통점은 컴파일러가 체크해주지 않으며 개발자가 경험으로 직접 예외처리 코드를 작성해야 한다는 점이다.

일반적으로 예외 발생 시 컴파일 시 오류가 발생한다.

하지만, 예외 처리 코드를 작성하는 것으로 오류 발생을 막을 수 있다.

### Try, Catch, Finally

예외처리 코드는 세 부분으로 나눌 수 있다.

1. `Try`
	- 실행되는 코드
2. `Catch`
	- 예외 발생시 처리하는 코드
	- `e.message` : 예외 발생시 출력되는 메세지
	- `e.stackTrace` : 예외 발생 지점
3. `Finally`
	- 예외 발생과 관계 없이 반드시 실행되는 코드. 생략 가능.

바로 위에서 다뤘던 `NullPointerException`이 발생하는 코드를 예외처리해보자.

**Kotlin**
```kotlin
fun upperStr(str: String?) {
    println("${str} has inputed.")
    try{
        println(str!!.toUpperCase())
    } catch (e: NullPointerException) {
        println("NPE occured! successfully catched.")
    } finally {
        println("finally upperStr done.")
    }
}

fun main() {
    upperStr(null)
    println()
    upperStr("hellllllo")
}
```

**Result**
```
null has inputed.
NPE occured! successfully catched.
finally upperStr done.

hellllllo has inputed.
HELLLLLLO
finally upperStr done.
```

`null`을 입력하여 `NullPointerException`이 발생하였음에도 실행이 종료되지 않고 성공적으로 try-catch-finally 순으로 예외처리에 성공하였다.

기존처럼, 문자열을 넣으면 `catch`는 발생하지 않고 try와 finally만 발생했다.

**Kotlin**
```kotlin
fun upperStr(str: String?) {
    println("${str} has inputed.")
    try{
        println(str!!.toUpperCase())
    } catch (e: Exception) {
        println("Exception occured! successfully catched.")
    } finally {
        println("finally upperStr done.")
    }
}

fun main() {
    upperStr(null)
}
```

**Result**
```
null has inputed.
Exception occured! successfully catched.
finally upperStr done.
```

이번에는 `catch`의 `e`의 변수형을 `NullPointerException`이 아닌, 그냥 `Exception`으로 바꿔주었다.

마찬가지로, 함수 자체는 잘 실행된다.

이는, `NullPointerException`이 `Exception`을 상속하기 때문이다.

좀 더 쉽게 말하면, `NullPointerException`은 `Exception`의 아주 작은 부분 중 하나 라는 의미다.

이 때문에, 여러 종류의 `Exception`을 동시에 처리해야 할 때, 작은 `Exception`부터 `catch`하도록 코딩을 해야한다.

극단적인 예시로, `Exception`을 맨 위에 catch하도록 구현해두고 그 밑에 수 십개의 하위 `Exception`들이 존재한다면, 모든 `Exception`이 맨 위에서 `catch`되기 때문에, 하위 예외처리들은 절대 발동하지 않는다.

그러므로, `Exception`간의 상속관계를 명확히 알고, 적절한 장소에서 `catch`하도록 코딩하는 방법을 반드시 익혀야 한다.

아래 예제를 통해 `e.message`와 `e.stackTrace` 를 직접 사용해서 결과를 살펴보고, 예외 처리를 간단히 마무리하자.

**Kotlin**
```kotlin
fun upperStr(str: String?) {
    println("${str} has inputed.")
    try{
        println(str!!.toUpperCase())
    } catch (e: NullPointerException) {
        println("${e.message} << message")
        println("${e.stackTrace} << stackTrace")
    } finally {
        println("finally upperStr done.")
    }
}

fun main() {
    upperStr(null)
}
```

**Result**
```
null has inputed.
null << message
[Ljava.lang.StackTraceElement;@23fc625e << stackTrace
finally upperStr done.
```



> 참고한 블로그 : [https://hongku.tistory.com/352](https://hongku.tistory.com/352)

