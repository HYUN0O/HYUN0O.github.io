---
categories: 
   - Android
tags: 
   - Kotlin
   - Android

---



 `kotlin-android-extensions` 플러그인을 프로젝트에서 제거하는 과정에서 해당 플러그인에 내장 된 기능인 `kotlin-synthetics`를 이용해 선언 된 `navHostFragment`를 `viewBinding`으로 마이그레이션하는 과정에서, 해당 Layout이 `<fragment/>`로 선언 되어있다는 점을 발견했다.

완전 옛날 기초 레이아웃인 그 `<fragment/>`!





## 기존 코드

```kotlin
// My Activity.kt
val navHostFragment = host_fragment as NavHostFragment
```

```xml
<!-- My Activity.xml -->
<fragment
        class="androidx.navigation.fragment.NavHostFragment"
        android:id="@+id/host_fragment"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        app:defaultNavHost="true"
        app:navGraph="@navigation/nav_app" />
```

`kotlin-android-extensions`에서 제공하는 `kotlin synthetics`를 사용하여 선언하였기에, 별도의 binding 변수를 통하지 않고 전역변수로 직접 접근하였다. (또한, 이 점이 구글이 `synthetics`를 deprecate 하게 만든 원인 중 하나이다.)



하지만 `kotlin synthetics`이 구글의 정책으로 인해 deprecate 예정이기에, `ViewBinding`으로 마이그레이션 할 필요가 있었다.



또한, 안드로이드 스튜디오에서는 오래된 레이아웃인 `<fragment/>`의 사용을 권장하지 않고,`<androidx.fragment.app.FragmentContainerView/>`를 사용하기를 권장하여 겸사겸사 수정해주기로 하였다.





## 처음 수정한 코드

```kotlin
// My Activity.kt
val navHostFragment = binding.hostFragment as NavHostFragment // This cast can never be succeed.
```

```xml
<!-- My Activity.xml -->
<androidx.fragment.app.FragmentContainerView
        class="androidx.navigation.fragment.NavHostFragment"
        android:id="@+id/host_fragment"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        app:defaultNavHost="true"
        app:navGraph="@navigation/nav_app" />
```

`binding`은 해당 Fragment의 Layout 파일을 `DataBindingUtil`을 통해 `DataBinding`시켜 둔 변수이다.

그런데 기존에 사용하던 Type Casting이 정상적으로 수행되지 않는 문제가 다시 발생하여 추가적인 조치를 취했다. 







## 문제 해결 코드

## 

```kotlin
// My Activity.kt
val navHostFragment = supportFragmentManager.findFragmentById(R.id.host_fragment) as NavHostFragment
```

```xml
<!-- My Activity.xml (위와 동일) -->
<androidx.fragment.app.FragmentContainerView
        class="androidx.navigation.fragment.NavHostFragment"
        android:id="@+id/host_fragment"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        app:defaultNavHost="true"
        app:navGraph="@navigation/nav_app" />
```

위와 같이 `SupportFragmentManager`에서 `findFragmentById`를 사용하여 직접 해당 FragmentContainerView를 검색하여, 해당 View가 지닌 Class 속성인 `navHostFragment`로 TypeCasting 하는 것으로 사용 가능하다.



이 문제는 새롭게 Navigation을 사용하는 개발자들은 겪을 일 없는 문제지만, 구버전 Navigation을 이미 사용중이였으나, 라이브러리 업데이트 등으로 인해 더 이상 기존 코드를 사용하기 힘든 개발자들은 이런 방법을 통해 문제를 해결할 수 있겠다.







## 참고 문서

[Android Developers - 탐색 구성요소 시작하기](https://developer.android.com/guide/navigation/navigation-getting-started#navigate)