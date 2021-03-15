---
categories: 
   - Android
tags: 
   - Kotlin
   - LiveData


---





Kotlin으로 Android 앱을 개발하다보면, 자연스럽게 LiveData, MutableLiveData를 자주 사용하게 된다.

그리고 이 LiveData의 Null 처리때문에 골머리를 썩는다.

보통 일어나는 LiveData와 Nullable의 충돌은 Kotlin과 LiveData의 환경부터 다르기 때문이다.

 

이 이유는 LiveData의 원본 소스를 한번 들여다보면 알 수 있다.

 

## 두 개념이 충돌하는 이유



MutableLiveData.java

```java
public class MutableLiveData<T> extends LiveData<T> {
    @Override
    public void postValue(T value) {
        super.postValue(value);
    }

    @Override
    public void setValue(T value) {
        super.setValue(value);
    }
}
```

 

LiveData.java

```java
...
	/**
     * Returns the current value.
     * Note that calling this method on a background thread does not guarantee that the latest
     * value set will be received.
     *
     * @return the current value
     */
    @Nullable
    public T getValue() {
        Object data = mData;
        if (data != NOT_SET) {
            //noinspection unchecked
            return (T) data;
        }
        return null;
    }
...
```

 

위 소스코드는 LiveData의 원본 소스중, 값을 반환하는 부분의 코드이다.

우리는 Data가 null일 경우, **null을 return 하도록 짜여있음**에 집중해야한다.

 



>  뭐? LiveData가 Nullable인건 이미 알고있는데?

하지만 그 배경을 조금 더 들여다보면 상관관계와 우리가 어떻게 대처해야 앞으로의 개발이 더 유연해질지 해결안이 보인다.



 

Java에서는 기본적으로 null을 허용하고, Kotlin에서는 기본적으로 null을 허용하지 않는다.

그러나, 위 소스는 Java 환경에서 환경에 어울리도록 구성되었기에 항상 null이 가능하도록 구현되어있다.



 

LiveData를 그대로 사용한다면, 사용할 때 마다 nullCheck를 진행해야하는 번거로움이 발생하는 문제가 있다.

우리는 LiveData를 우리가 쓰는 Kotlin에 맞도록 기본적으로 null을 **허용하지 않도록 수정**하여 매번 발생하는 번거로움을 줄이는 방안을 고민해봄직하다.



 

아래 클래스를 확인해보자.

 

##  NonNullMutableLiveData.kt 

NonNullMutableLiveData.kt

```kotlin
class NonNullMutableLiveData<T : Any>(defaultValue: T) : MutableLiveData<T>() {

    init {
        value = defaultValue
    }

    override fun getValue()  = super.getValue()!!
}
```

 



원리는 복잡하지 않다.

단순히 LiveData에서 Null을 return 할 수 있도록 허용해주던 `getValue()`를  !! 연산자를 통해 null을 return 할 수 없도록 제한하면 된다.

 

## 개선 된 코드

![그림1](https://github.com/donggi9313/donggi9313.github.io/blob/master/assets/image/20210312/01.jpg?raw=true)



 

위 이미지에서 확인할 수 있듯이 간단한 코드만으로 빌드 전에 미리 null check를 수행할 수 있어졌다.





아래는 NonNullMutableLiveData를 사용함으로 얻을 수 있는 이점을 한 눈에 볼 수 있도록 작성한 예시다.

외출하기 전에 스스로의 복장을 검사하는 느낌의 함수로 작성하여 보았다.





## 예시



```kotlin
    var shirt: MutableLiveData<String> = MutableLiveData()
    var pants: MutableLiveData<String> = MutableLiveData()	

    fun clothCheck() {
        when {
            shirt.value?.isNotEmpty()?:false && pants.value?.isNotEmpty()?:false -> {
                wearCloths()
            } shirt.value?.isEmpty() == true -> {
                wearShirts()
            } pants.value?.isEmpty() == true -> {
                wearPants()
            } else -> {
                goOut()
            }
        }
    }
```

1. `shirt.value`가 null 일 수 있기에 **nullCheck**
2. `shirt.value`가 null이면 `shirt.value?.isNotEmpty()`또한 null이 되므로, **nullCheck**
3. `shirt.value?.isEmpty()`가 true, false, 그리고 **null**일 수 있으므로 boolean 형 임에도 불구하고 `== true`를 **굳이** 사용해야함.



굉장히 번거롭게 null을 반복적으로 check 해줘야하며, 코드의 가독성 또한 굉장히 떨어진다.



아래는 `MutableLiveData`를 Null을 사용 할 수 없도록 수정한 `NonNullMutableLiveData`로 위의 함수를 재구성한 코드다.



```kotlin
    var shirt: NonNullMutableLiveData<String> = NonNullMutableLiveData("")
    var pants: NonNullMutableLiveData<String> = NonNullMutableLiveData("")
    
    fun clothCheck() {
        when {
            shirt.value.isNotEmpty() && pants.value.isNotEmpty() -> {
                wearCloths()
            } shirt.value.isEmpty() -> {
                wearShirts()
            } pants.value.isEmpty() -> {
                wearPants()
            } else -> {
                goOut() 
            }
        }
    }
```



한 눈에도 차이를 알 수 있다.

번거로운 nullCheck도 굉장히 많이 줄어들었으며 boolean임에도 `== true`를 사용 할 필요도 없어졌다. 무야호~







 

## 참고자료

[Java Value는 언제나 null이다.](https://thdev.tech/kotlin/2020/11/24/kotlin_effective_12/)