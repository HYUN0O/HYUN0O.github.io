---
categories: 
   - Android
tags: 
   - Kotlin
   - Android
   - TDD

---



TDD를 통한 개발을 위해 가장 먼저 준비 할 것은 '테스트 케이스 작성'입니다.

테스트는 크게 2가지로 나눌 수 있습니다.

- [UI 테스트](https://developer.android.com/training/testing/ui-testing)
- [단위 테스트](https://developer.android.com/training/testing/unit-testing)



오늘은 그 중 'UI 테스트 자동화'에 대해 공부하고 테스트한 내용에 대해 글을 남기려고 합니다.





## Implement

```groovy
dependencies {
    androidTestImplementation 'androidx.test.espresso:espresso-core:3.1.0'
}
```

2021년 7월 7일 현재, 안정화 된 Espresso 버전은 3.1.0 입니다.





## 애니메이션 사용 중지

Espresso는 구현한 동작을 빠르게 수행하기에 애니메이션 동작이 있는 경우, 원활한 테스트에 문제가 생길 수 있습니다.

물론 `Delay`를 걸어서 테스트 하는 것도 방법이지만, 애니메이션 동작 시간이라도 변하면 일일히 수정하는 것도 또 다른 자원의 낭비이기에 사용 중지를 권장하고 있습니다.

Android 개발자 문서에서는 테스트용 기기의 애니메이션을 사용 중지 하는 방법을 권하나, 프로젝트의 Gradle에서 테스트 할 때만 Animation을 비활성화 시키는 방법을 지원하므로 저는 아래처럼 후자의 방법을 사용합니다.



```groovy
// build.gradle (project)
android {
    testOptions {
        animationsDisabled = true
    }
}
```







## 테스트 클래스 구현



UI 구성 요소에 접근하는 방법은 기본적으로 아래와 같습니다.



```kotlin
    onView(withId(R.id.my_view))            // withId(R.id.my_view) is a ViewMatcher
            .perform(click())               // click() is a ViewAction
            .check(matches(isDisplayed()))  // matches(isDisplayed()) is a ViewAssertion
```

1. ViewMatcher: 원하는 View를 찾습니다.
2. ViewAction: View에서 어떤 동작을 수행할 지 결정합니다.
3. ViewAssertion: 원하는 동작이 수행 되었는지 체크한다. 하나라도 Check에 실패하면 테스트는 실패합니다.

ViewMatcher는 생략될 수 없으며, ViewAction이나 ViewAssertion은 생략 될 수 있습니다.





### ViewMatcher 지정

ViewMatcher을 지정하는 방법은 크게 3가지가 있습니다.

1. 텍스트 문자열을 통한 검색 : `ViewMatchers.withText("Sign-in")`
2. 뷰의 ID를 통한 검색: `ViewMatchers.withId(R.id.button_signin)`
   - 중복되는 ID를 검색하는 경우, Exception을 발생시킵니다.
3. 두 가지 이상의 조건을 중첩시켜 검색: `onView(allOf(withId(R.id.button_signin), withText("Sign-in")))`
   -  라이브러리에서 지원합니다.



일반적으로, `onView(ViewMatcher)`의 형태로 View에 접근합니다.





### ViewAction 실행

ViewActions는 `onView(ViewMatcher)`의 멤버 함수와 같이 아래와 같은 형태로 사용됩니다.

`onView(ViewMatcher).perform(ViewAction)`

1. `ViewActions.click()`: 뷰를 클릭 합니다.
2. `ViewActions.typeText(String)`: String을 입력합니다.
3. `ViewActions.scrollTo()`: 해당 뷰가 있는 곳 까지 스크롤합니다.
4. `ViewActions.pressKey(int)`:입력한 keyCode(int)에 대응하는 키를 누릅니다.
5. `ViewActions.clearText()` : 해당 뷰에 적힌 텍스트를 지웁니다.

구글 안드로이드 디벨로퍼즈에 명시된 기능만 작성하였으며, 이 외에도 다양한 기능이 있지만 저는 아직 사용해보지 못했습니다.



### ViewAssertion 확인

원하는 결과를 잘 얻었는지 확인할 수 있습니다.

ViewAssertion은 `onView(ViewMatcher)`의 멤버 함수와 같이 아래와 같은 형태로 사용됩니다.

`onView(ViewMatcher).check(ViewAssertion)`

- `ViewAssertions.doesNotExist() `: 지정한 뷰가 없는지 확인합니다.
- `ViewAssertions.matches()`: 지정한 뷰가 있으며, 원하는 상태인지 확인합니다.





## 테스트 진행



예를 들어 아래와 같은 순서로 테스트를 진행하고 싶다고 가정해보겠습니다.

1. `et_item_name`라는 id를 갖는 `<EditTest .../>`에 특정 문자열을 입력합니다.
2. `추가하기`라는 문자열이 적힌 버튼을 클릭합니다.
3. 정상적으로 등록이 되었다면, 화면에서 입력한 문자열을 확인할 수 있습니다.



이럴 땐 아래와 같이 테스트를 구현하면 됩니다.



```kotlin
@Test
fun myTest() {
    Espresso.onView(ViewMatchers.withId(R.id.et_item_name))
        .perform(ViewActions.typeText("TEST STRING"))

    Espresso.onView(ViewMatchers.withText("추가하기"))
        .perform(ViewActions.click())

    Espresso.onView(ViewMatchers.withText("TEST STRING"))
        .check(ViewAssertions.matches(ViewMatchers.isDisplayed()))
}
```



충분히 이해가 되었다면, 본인이 만든 앱의 기초 로직을 이용하여 간단한 테스트를 구현할 수 있을겁니다.

제 경우에는 내게 필요한 기술이기에 글만 읽고 끝내지 않고 직접 적용해보니 더 쉽게 익힐 수 있었습니다.





## 참고 문서

[Android Developers - 단일 앱의 UI 테스트](https://developer.android.com/training/testing/ui-testing/espresso-testing)

[Espresso Testing Samples](https://github.com/android/testing-samples)