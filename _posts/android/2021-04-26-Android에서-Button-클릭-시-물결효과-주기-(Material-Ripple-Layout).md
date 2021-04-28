---
categories: 
   - Android
tags: 
   - Kotlin
   - Android
   - Rx
---









버튼을 클릭 할 때, 피드백이 있으면 사용자는 '내가 이 버튼을 누르고있구나' 라는 사실을 쉽게 알아챌 수 있다.

방법은 여러가지로 할 수 있으나, 일반적으로는 소리 또는 진동을 통해, 그리고 UI를 통해서 사용자에게 정보를 전달하는 경우가 많다.

오늘은 UI를 통해 사용자에게 피드백을 주는 방법 중 하나인 Material Ripple Layout에 대해 알아보고, 직접 사용 해보려고 한다.





| ![feedback_X](https://github.com/donggi9313/donggi9313.github.io/blob/master/assets/image/20210426/feedback_X.gif?raw=true) | ![feedback_O](https://github.com/donggi9313/donggi9313.github.io/blob/master/assets/image/20210426/feedback_O.gif?raw=true) |
| :----------------------------------------------------------: | :----------------------------------------------------------: |
|                      피드백이 없는 경우                      |                      피드백이 있는 경우                      |

한 눈에 봐도 알 수 있듯이, 사용자에게 피드백의 유무는 조작에 있어 꽤 큰 차이가 있다.

특히 사용자가 터치스크린 조작에 익숙하지 않거나, 애매한 위치를 클릭 했을 때 어느 버튼을 클릭 했는지 확신을 심어 줄 수 있다.







## Project에 Import 하기



```groovy
dependencies {
    ...
    implementation 'com.balysv:material-ripple:1.0.2'
    ...
}
```

App 단계의 Gradle에 위 코드를 사용하여 라이브러리를 import 하자.





## 적용하기



내 앱에 적용하는 방법은 크게 2가지가 있다.

1. 첫 번째는 `View`에서 정적 초기화를 통해 할당하는 방법.
2. 두 번째는 `Layout`에서 `MaterialRippleLayout`으로 적용할 View를 감싸는 방법.



두 방법 모두 결과적으로 차이는 없으니, 본인이 선호하는 방법으로 구현하면 된다.







### View에서 정적 초기화하기



```xml
<!-- main_fragment.xml -->

<androidx.appcompat.widget.AppCompatImageView
    android:id="@+id/btn_setting"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:layout_gravity="center"
    android:src="@drawable/baseline_settings_24"
    android:tint="@color/f1"
    android:padding="7dp"
    android:onClick="@{(v) -> vm.onClick(v)}"
    />
```

`btn_setting`라는 이름을 지닌 Layout에 Ripple 효과를 주고 싶다면, View에서 아래와 같은 작업을 해주면 된다.





MainFragment.kt

```kotlin
// MainFragment.kt
    
override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
    super.onViewCreated(view, savedInstanceState)
    binding.vm = getViewModel()
    binding.lifecycleOwner = viewLifecycleOwner
    
    initUi()
}

   
private fun initUi() {
    MaterialRippleLayout.on(binding.btnSetting)
        .rippleOverlay(true)
        .rippleDuration(250)
        .create()
}
```



기타 설정 값들은 글 최하단, 라이브러리를 만든 개발자의 readme.md를 참고하면 된다.







### Layout에서 MaterialRippleLayout으로 감싸기



```xml
<!-- 수정 전 main_fragment.xml -->

<androidx.appcompat.widget.AppCompatImageView
    android:id="@+id/btn_setting"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:layout_gravity="center"
    android:src="@drawable/baseline_settings_24"
    android:tint="@color/f1"
    android:padding="7dp"
    android:onClick="@{(v) -> vm.onClick(v)}"
    />
```

`btn_setting`라는 이름을 지닌 Layout에 Ripple 효과를 주고 싶다면, 말 그대로 `MaterialRippleLayout`으로 해당 Layout을 감싸면 된다.

아래 수정 된 코드를 확인해보자.





```xml
<!-- 수정 후 main_fragment.xml -->

<com.balysv.materialripple.MaterialRippleLayout
    android:layout_width="0dp"
    android:layout_weight="1"
    android:layout_height="match_parent"
    app:mrl_rippleOverlay="true"
    app:mrl_rippleDuration="250"
    >

        <androidx.appcompat.widget.AppCompatImageView
        android:id="@+id/btn_setting"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:layout_gravity="center"
        android:src="@drawable/baseline_settings_24"
        android:tint="@color/f1"
        android:padding="7dp"
        android:onClick="@{(v) -> vm.onClick(v)}"
        />

</com.balysv.materialripple.MaterialRippleLayout>
```

단순히 뷰를 감싸는 것 만으로 해결된다.







## 주의할 점



주의힐 점이라면, **`onClick`을 `MaterialRippleLayout`에 연동하면 안된다**는 점이다.

이 이유는 아래 코드를 보면 알 수 있다.



```java
// MaterialRippleLayout.java

@Override
public void setOnClickListener(OnClickListener onClickListener) {
   if (childView == null) {
       throw new IllegalStateException("MaterialRippleLayout must have a child view to handle clicks");
   }
    childView.setOnClickListener(onClickListener);
}
```

`MaterialRippleLayout`은 onClickEvent로 하위 Layout의 onClickEvent를 작동시키도록 되어있다.

그러니, 기존 Layout의 onClickEvent를 `MaterialRippleLayout`으로 옮길 필요는 없다.





## 추가

위와 같이 복잡하게 라이브러리를 import하거나 할 필요 없이, 효과를 주고 싶은 layout에서 `android:background="?attr/selectableItemBackground"` 속성       을 지정하는 것으로도 ripple 효과는 쉽게 줄 수 있다.

하지만 이 방법은 안드로이드의 버전에 따라 그려지는 view가 달리 보일 수 있기에, 자신이 만드는 앱이 지원하는 안드로이드의 **버전**과 지향하는 디자인에 따라서 선택해서 사용하면 된다.









## 참고

- https://github.com/balysv/material-ripple

