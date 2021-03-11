---
categories: 
   - Android
tags: 
   - Databinding
   - MVVM
   - ViewPager2
   - Koin
---







오늘은 안드로이드에 MVVM 디자인 패턴을 적용하기 위해, DataBinding을 프로젝트에 적용하는 과정을 기록해두려고 한다.

내가 구현하고자 하는 프로젝트의 대략적인 구조는 아래와 같다.

- MVVM 디자인 패턴
  - Koin 라이브러리를 통해 ViewModel에 의존성 주입
  - DataBinding을 통한 가독성 높고 사용하기 편한 코드 작성
- 3개의 Tab를 ViewPager2로 구현
  - 각 Tab마다 RecyclerView를 지니며, 각각 Header, Footer, 모두를 Sticky하게 구현







# Koin 적용

Koin은 앱에 의존성 주입을 간단히 할 수 있도록 돕는 라이브러리다.

사용자의 수준이나 요구에 따라, DataBase, Glide, Retrofit, OkHttp등, 이외에도 다양한 모듈을 쉽게 만들고 관리할 수 있는 뛰어난 라이브러리이다.

현재 작성 중인 예제의 수준에서는 `ViewModelModule`를 통해 앱 내의 ViewModel을 일괄로 관리하고, 의존성을 부여하는 간단한 수준에서 사용하려고 한다.





우선, Koin과 DataBinding으로 앱의 기초 구조를 다지고 시작하려고 한다.

그 중 DataBinding부터 적용해보자.





## App 수준의 Gradle 파일 수정

build.gradle (:app)

```groovy
dependencies {
	// Koin - DI
    implementation "org.koin:koin-core:2.0.1"
    implementation "org.koin:koin-android:2.0.1"
    implementation "org.koin:koin-androidx-viewmodel:2.0.1"
}
```

MVVM 패턴을 사용하는 앱에서는, koin에서 제공하는 viewmodel 라이브러리까지 함께 implement하면 더욱 효율적으로 패턴을 관리할 수 있다.





## ViewModelModule .kt 구현

ViewModelModule.kt

```kotlin
package com.example.recyclerview_sticky_headerfooter_example.di

import com.example.recyclerview_sticky_headerfooter_example.ui.main.MainTabViewModel
import com.example.recyclerview_sticky_headerfooter_example.ui.main.MainViewModel
import org.koin.androidx.viewmodel.dsl.viewModel
import org.koin.dsl.module

val ViewModelModule = module {
    viewModel { MainViewModel() } /* 1 */
} 
```

Module이란, 객체를 생성하여 의존성 주입을 진행 할 대상들에 대한 목록이라고 생각할 수 있다.

현재는 ViewModel에 별도의 생성자가 존재하지 않지만, Application, API, Prefrence 등을 생성자로 추가하고, Module에서 `get()`을 통해 호출하여 ViewModel에서도 쉽게 사용하도록 확장할 수 있다.

1. ViewModel에 생성자가 있을 경우, Koin 모듈이 객체를 생성하여 주입하도록 사용할 수 있다.
   - `viewModel { MainViewModel(get()) }`





## App.kt 구현

App.kt

```kotlin
class App: Application() {
    override fun onCreate() {
        super.onCreate()
        startKoin {
            androidLogger()
            androidContext(this@App)
            modules(ViewModelModule) /* 1 */
        }
    }
}
```

Application 클래스에서 Koin을 위와 같이 start 할 수 있다.

혹은, 자신이 이미 사용하는 Application Class가 있다면, `startKoin { ... }`만 추가하면 된다.

1. `listOf()` 함수를 사용하여 둘 이상의 Module를 사용 할 수 있다.
   - `modules(listOf(networkModule, ViewModelModule))`





## 만든 App.kt를 Manifest에 연동하기

AndroidManifest.xml

```xml
 <application
        android:name=".App"
        ...
```

기본적으로  AndroidManifest.xml 파일에는 application이라는 태그로 이루어진 부분에, name 패러미터만 추가로 입력해주면 된다.

이를 통해, 앱 시작과 동시에 startKoin() 함수를 실행하게 된다.





# DataBinding 적용

Koin과 함께 DataBinding을 사용하는 것으로, MVVM 패턴에 날개를 달아줄 수 있다.





## App 수준의 Gradle 파일 수정

build.gradle (:app)

```groovy
plugins {
    id 'kotlin-kapt'
}
android {
	buildFeatures {
        dataBinding = true
    }
}
```

dataBinding을 사용하기 위해서는, kapt 플러그인을 사용하는 상태여야한다. 





## DataBinding을 적용 할 Layout 파일을 \<layout\> 태그로 감싸기 

activity_main.xml 

```xml
<?xml version="1.0" encoding="utf-8"?>
<layout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools">
    
    <!-- (1) ↓기존 코드↓ -->
    <FrameLayout
        android:id="@+id/fragment"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        tools:context=".ui.main.MainActivity" />
    <!-- (1) ↑기존 코드↑ -->

</layout>
```

1. 이 과정에서, 기존 **최상단 태그**<sub>(예시에서는 FrameLayout)</sub>에 있던 \<xmlns:.....\> 태그들은 **모두 제거**해주어야 한다.

   한 레이아웃 당, 동일한 xmlns 태그는 유일해야하기 때문에, 제거하지 않으면 빌드 시 에러가 발생한다.





## xml 파일 내에 ViewModel 데이터 정의하기

activity_main.xml 

```xml
<?xml version="1.0" encoding="utf-8"?>
<layout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools">
    
    <!-- (1) ↓데이터 정의↓ -->
    <data>
        <variable
            name="vm"
            type="com.example.recyclerview_sticky_headerfooter_example.ui.main.MainViewModel"/>
    </data>
    <!-- (1) ↑데이터 정의↑ -->

    <!-- ↓기존 코드↓ -->
    <FrameLayout
        android:id="@+id/fragment"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        tools:context=".ui.main.MainActivity" />
    <!-- ↑기존 코드↑ -->

</layout>
```

위와 같이 데이터를 정의 하면, xml 파일 내에서 `@{vm}`이라는 코드를 작성하는 것으로, ViewModel 내의 데이터를 사용할 수 있다.

1. xml 파일 내에서 데이터를 정의할 수 있다. 정의해둔 데이터는 Activity, Fragment에서 할당해주는 것으로 xml 파일 내에서도 자유롭게 사용할 수 있다.





## BindingActivity, BindingFragment 구현하기

DataBinding된 xml 내의 태그들을 id로 간편하게 찾기 위해서는 구현되는 모든 Activity, Fragment마다 바인딩 된 Layout 파일의 ResourceID를 통해 Layout을찾는 과정을 거쳐야한다.

하지만 이후에 구현할 Activity, Fragment에서 아래 클래스들을 구현해두고, 상속받는 것으로 `binding`만 입력하는 것으로 바로 Layout파일에 접근할 수 있다.



BindingActivity.kt

```kotlin
abstract class BindingActivity<T: ViewDataBinding> : AppCompatActivity() {
    @LayoutRes
    abstract fun getLayoutResId(): Int

    protected lateinit var binding: T
        private set

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        binding = DataBindingUtil.setContentView(this, getLayoutResId())
        
    }
} 
```



BindingFragment.kt

```kotlin
abstract class BindingFragment<T: ViewDataBinding> : Fragment() {
    @LayoutRes
    abstract fun getLayoutResId(): Int

    protected lateinit var binding: T
        private set

    override fun onCreateView(
        inflater: LayoutInflater,
        container: ViewGroup?,
        savedInstanceState: Bundle?
    ): View? {
        return DataBindingUtil.inflate<T>(inflater, getLayoutResId(), container, false).apply{ binding = this }.root
    }
}
```

이후 작성하는 Class들은, 아래와 같이 위 클래스를 상속받는 것으로 쉽게 사용할 수 있다.





MainFragment.kt

```kotlin
class MainFragment : BindingFragment<FragmentMainBinding/* 1 */>() {

    private lateinit var mVM: MainViewModel

    @LayoutRes
    override fun getLayoutResId() = R.layout.fragment_main	/* 2 */
    
    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        super.onViewCreated(view, savedInstanceState)
        binding.vm/* 3 */ = getViewModel()
        binding.lifecycleOwner = viewLifecycleOwner
    }
}
```

1. `FragmentMainBinding`은, `fragment_main.xml`파일이 DataBinding를 통해 Bind되어있는 View를 의미한다.
2. 해당 Fragment가 의미하는 Layout 파일의 이름을 그대로 입력해주면 된다.
3. xml 파일에서 데이터 정의해둔 vm에 해당 Fragment의 ViewModel을 연동해주는 과정.





여기까지 성공적으로 진행했다면, MVVM 패턴으로 앱을 개발하기 위한 사전준비는 끝났다고 할 수 있다.

다음 게시글에는 ViewPager2로 MainFragment에 있는 ViewPager로 이루어진 3개의 Tab Fragment를 리팩토링 해보려고 한다.





## Raw Code

RawCode는 [여기](https://github.com/danggai/Recyclerview_Sticky_HeaderFooter_Example)에서 확인할 수 있다.

그 중, [이 커밋](https://github.com/danggai/Recyclerview_Sticky_HeaderFooter_Example/commit/32c9750cec24565c8b9743e0ac9deffb6bd6f564)에 해당하는 내용이 이 글의 대부분의 내용이다.

