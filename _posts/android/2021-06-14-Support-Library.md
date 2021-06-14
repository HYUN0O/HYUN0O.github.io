---
categories: 
   - Android
tags: 
   - Kotlin
   - Android

---





## 지원 라이브러리

[지원 라이브러리](https://developer.android.com/topic/libraries/support-library?hl=ko)는, 이제는 [AndroidX](https://developer.android.com/jetpack/androidx?hl=ko)에 병합되었지만, 그 기능은 그대로 유지하고있다.

안드로이드가 발전함에 따라 여러 함수들에 필연적으로 변화가 생긴다.

모든 개발자가 이러한 내역들을 잘 알고 안드로이드 버전에 따라 처리를 할 수 있다면 좋겠지만, 비효율적이고 그럴 수도 없다.

이에 구글은 **알아서 안드로이드 버전에 대응하여 필요한 형태로 리턴하도록** 라이브러리의 형태로 지원하고있다.



## 사용 방법

기존에는 사용하기 위해 지원 라이브러리를 gradle에 추가해주어야 했으나, AndroidX로 편입되며 Android Jetpack에 통합되었다.

새로운 프로젝트를 생성하면 AndroidX는 기본으로 사용하도록 설정되어있다.

그럼에도 불구하고, 기존 support Library를 사용하던 개발자는 [AndroidX로 이전](https://developer.android.com/jetpack/androidx/migrate?hl=ko) 을 참고하여 개발을 진행할 수 있다.





### 사용 예시

예를 들어, Context 내의 `getSystemService`는 Android 23 이상에서는 `serviceClass: Class<T>`를 필요로 하지만, 미만에서는 `serviceName: String`을 필요로 한다.



이에 대해 `ContextCompat.java`라는 지원 라이브러리에서는 아래와 같은 형태로 대응한다.

```java
@Nullable
public static <T> T getSystemService(@NonNull Context context, @NonNull Class<T> serviceClass) {
    if (Build.VERSION.SDK_INT >= 23) {
        return context.getSystemService(serviceClass);
    }
    String serviceName = getSystemServiceName(context, serviceClass);
    return serviceName != null ? (T) context.getSystemService(serviceName) : null;
}
```





사용할 수 있는 모든 메서드는 [Android Developers - CompatContext](https://developer.android.com/reference/androidx/core/content/ContextCompat#public-methods_1) 를 참고하면 된다.

주로 Color, Drawable, Permission Check 등을 사용한다.