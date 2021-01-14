---
categories: 
   - Study
tags:
   - Kotlin
---
오늘은 명시적, 묵시적 Type Casts에 대해 다루어보려고 한다.

이후에 나오는 코드들은 [Kotlin Playground](https://play.kotlinlang.org/)에서 직접 입력하고, 실행하며 학습하면 도움이 된다!

바로 본론으로 넘어가겠다.

# Kotlin
## ☝ Type Casts

스마트 캐스트란, 어떤 값이 여러가지 형일 수 있는 경우에서, 컴파일러가 자동으로 판단하여 타입 캐스팅을 수행하는 기능이다.

타입 캐스팅이란, 자료형을 다른 형태로 변경하는 것을 의미한다.

스마트 캐스트가 적용되는 자료형은 하위 자료형을 갖는 자료형, 즉 Number, Any 등이 존재한다.

### 1.1 Smart Cast

스마트 캐스트란, 어떤 값의 타입이 여러가지로 나뉠 수 있을 때, 컴파일러가 자동으로 유효한 타입 캐스팅을 수행하는 기능을 의미한다.

```kotlin
var number: Number = 12
```

위의 코드에서, `number`은 자동으로 Int형으로 스마트캐스트 된다.

```kotlin
var number: Number
number = 12
```

만약 위 처럼 값을 할당해주지 않고 변수를 선언만 한다면 이야기가 조금 달라진다.

선언된 변수에 값을 할당할 때는 자료형이 변하지 않지만, 이후 자료형이 호출될 때 스마트 캐스트가 수행된다.

이렇게 스마트 캐스트를 이용한 묵시적 캐스팅 외에도 `as`를 이용한 명시적 타입 캐스트 방법도 존재한다.

### 1.2 Type Cast (as)

`as`는 명시적 type casting을 수행하는 가장 기본적인 명령어다.

하지만 as는 기본적이므로, 다른 명령어보다는 unsafe한 명령어다.

아래의 예제를 살펴보자.

**Kotlin**
```kotlin
fun main() {
    val x = 123
    val int :Int = x as Int
    println("int = $int")
}
```

**Result**
```
int = 123
```

x는 선언할 때 Float형 인지, Double형 인지, Int형 인지 선언되지 않았기에, 최초에 Number형으로 선언되어있다.

이후, x를 Int형으로 타입 캐스트 하여 int라는 변수에 할당하였다.

이는 Number형이 Int형의 상위에 있는 타입이기에 오류 없이 실행이 가능하다.

하지만, 이는 형식이 맞지 않는 경우에는 오류가 발생한다.

아래는 오류가 발생하는 예제이다.

**Kotlin**
```kotlin
fun main() {
    val x :Any = "123"
    val int :Int = x as Int
    println("int = $int")
}
```

**Result**
```
Exception in thread "main" java.lang.ClassCastException: class java.lang.String cannot be cast to class java.lang.Integer (java.lang.String and java.lang.Integer are in module java.base of loader 'bootstrap')
	at MainKt.main(Main.kt:3)
	at MainKt.main(Main.kt)
```

이런 식으로 클래스 캐스트 예외가 발생한다.

이런 경우에 catch를 통한 예외처리 역시 물론 가능하지만, `as?`를 이용하여 예외처리가 된 채로 사용할 수도 있다.

### 1.3 Safe Type Cast (as?)

`as`명령어는 올바르지 않은 클래스를 캐스팅하면 바로 예외가 발생한다.

하지만, `as?`명령어는 올바르지 않은 클래스를 캐스팅하면 `Null`을 반환하도록 이미 예외처리가 된 명렁어이다.

아래 예제를 통해 알아보자.

**Kotlin**
```kotlin
fun main() {
    val x :Any = "123"
    val int :Int? = x as? Int
    println("int = $int")
}
```

**Result**
```
int = null
```

x가 Int로 형변환이 불가능한 형식일 때, `x as? Int`는 `Null`을 return하였다.

`as?`를 `as`와 `catch`를 통해 구현하면 아래와 같다.

**Kotlin**
```kotlin
fun isInt(origin: Any): Int? {
    try {
        val result:Int = origin as Int
        return result
    } catch (e: ClassCastException) {
        return null
    }
}

fun main() {
    val x :Any = "123"
    val int :Int? = x as? Int
    println("int = $int")
}
```

**Result**
```
int = null
```

이정도 되는 코드를 `?`하나로 퉁쳐버린 코틀린... 정말..

이처럼 `as?`를 사용하면 `as`만 이용하는 것에 비해 더욱 간결하고 안전하게 타입 캐스팅을 수행할 수 있다.


### 1.4 Type Check (is)

`is` 연산자를 이용하면 변수의 타입을 확인할 수 있다.

물론 이 역시 expression이어서 true/false 형식으로 값을 반환한다. 그러므로, when문을 이용하여 케이스별로 나누어서도 정의할 수 있다.

아래 예시는 number의 타입에 따라 다른 값을 프린트하는 함수의 예시이다.

**Kotlin**
```kotlin
fun check(number: Any) {
    when {
        number is Int -> println("$number is Integer.")
        number is Double -> println("$number is Double.")
        number is Float -> println("$number is Float.")
        else -> println("$number is not number.")
    }
}

fun main() {
    check(12.345)
    check(12)
    check("hello")
}
```

**Result**
```
12.345 is Double.
12 is Integer.
hello is not number.
```

is의 부정형, `!is`도 존재한다.

is는 값이 해당 타입일 때 `True`를 반환하지만,  `!is`는 값이 해당 타입이 아닐 때 True를 반환한다.

아래 예시를 통해 알아보고, 타입 캐스트에 대한 이야기는 

**Kotlin**
```kotlin
fun check(input: Any) {
    when {
        input is Number && input !is String -> println("$input is not String.")
        input is String && input !is Number -> println("$input is not Number.")
        else -> println("$input unknown Type.")
    }
}

fun main() {
    check(123)
    check("hello")
    check(listOf(1,2,3))
}
```

**Result**
```
123 is not String.
hello is not Number.
[1, 2, 3] unknown Type.
```



