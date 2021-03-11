

# 개요

Kotlin으로 Android 앱을 개발하다보면, 자연스럽게 LiveData, MutableLiveData를 자주 사용하게 된다.

그리고 이 LiveData의 Null 처리때문에 골머리를 썩는다.

보통 일어나는 LiveData와 Nullable의 충돌은 Kotlin과 LiveData의 환경부터 다르기 때문이다.





이 이유는 LiveData의 원본 소스를 한번 들여다보면 알 수 있다.





# 두 개념이 충돌하는 이유



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



#  NonNullMutableLiveData.kt 

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



# 변경 된 코드

![그림1](https://github.com/donggi9313/donggi9313.github.io/blob/master/assets/image/20210312/01.jpg?raw=true)





위 이미지에서 확인할 수 있듯이 간단한 코드만으로 빌드 전에 미리 null check를 수행할 수 있어졌다. 무야호~





# 참고자료

[Java Value는 언제나 null이다.](https://thdev.tech/kotlin/2020/11/24/kotlin_effective_12/)