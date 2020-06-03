---
categories: 
  - study
tags:
   - Android
---

개발 중, 같은 코드 및 같은 API 버전임에도 불구하고, 다르게 동작하는 코드가 있어서 며칠간 골머리를 썩었다.

`동작 시도 -> 웹뷰 -> 타 앱으로 이동 -> 타 앱과의 연동 성공 -> 비밀번호 입력 -> 동작 완료`순으로 동작하기를 바랬으나, 특정 기기에서는 `웹뷰 -> 타 앱으로 이동 -> 타 앱과 연동 성공 -> 동작 시도 전의 상태`로 돌아가는 문제가 발생했다.

해당 오류를 해결하기 위해 꽤 오랫동안 골머리를 썩혔지만, 막상 해결하니 몇 줄의 변화가 전부였다.


## 문제점

웹뷰와 패스워드창의 자동종료를 위해 사용한 코드는 'supportFragmentManager.popBackStack(String name, int flags)' 코드였다.

name에는 fragment를 구분할 수 있는 문자열을 넣었으며, flags에는 `POP_BACK_STACK_INCLUSIVE`가 들어있었기에, 해당 name를 만족하는 fragment가 존재할 때, 해당 fragment를 종료하는 행동을 수행했다.

popBackStack을 사용하니 기기에 따라 fragment를 pop하는 타이밍에 조금씩 차이점이 있었다.

그렇다보니 해당 fragment가 생명주기에 따라 `onCreate -> onStart -> onResume`까지 도달하기도 전에 pop을 수행해버리니, 해당 fragment에서 생명주기 함수들을 override하여 로그를 찍어 어디에서 문제가 생기는지 알아보고자 했을 때 onResume() 함수는 로그조차 남지 않았기에 곤란했다.


나중에 천천히 코드를 다시 살펴보니, 아래와 같았다.
- 정상적인 작동 순서: *메인 fragment -> webView fragment 실행 -> webView fragment 종료 -> passwordInput fragment 실행 -> 메인 fragment* 순으로 일어나기를 기대함.
- 비정상적인 작동 순서: *메인 fragment -> webView fragment 실행 -> **passwordInput fragment가 onStart까지만 실행 (고로 보이지는 않음) -> webView fragment 종료** -> 메인 fragment로 돌아옴* 순으로 일어나는 문제를 확인하였다.

비정상적인 작동의 경우, webView fragment를 종료하는 함수가 `popBackStack(webViewFragment.TAG, POP_BACK_STACK_INCLUSIVE)`이기 때문에, 앱 위에 `main -> webView -> passwordInput` 순으로 올라가있는 뷰들이 webView까지 한번에 종료되며 passwordInput까지 종료되어 발생하는 문제임을 알 수 있었다.

## 해결 방법

`popBackStack()`이 아닌, `popBackStackImmediate()`를 대신해서 사용하였다.

다른 fragment에서는 `popBackStack()`이 문제 되는 경우가 아직 없었지만, 이번 경우처럼 **기존 뷰-웹 뷰-패스퉈드 입력** 처럼 두 fragment 사이에 끼인 채로 작동하는 fragment에서는 명확한 타이밍에 pop 처리가 되지 않으면, 문제가 발생할 수 있음을 깨달았다.

또한, `popBackStackImmediate()`의 경우, try-catch문으로 예외처리를 해주지 않으면 자주 비정상 종료가 발생했다.

## 결론

1. `popBackStack()`은 비동기적으로 작동하는 함수다.
    - 다음 loop cycle에서 pop이 수행됨.
    - 즉, FragmentTransaction이 수행된 후에는 백스택에서 **팝되지 않음**.
    - 물론 실제 사용 환경에서는 다음 loop cycle 역시 매우 빠르게 도달하기에, 사용자는 동작의 차이를 느끼기 힘듬.
  
2. `popBackStackImmediate()`은 지금 즉시 작동하는 함수다.
    - 지금 즉시 수행하지만, 기기에 따라 성능적인 문제를 야기할 수 있음.
    - 타 함수와 연계되는 등, **정확한** 타이밍에 반드시 일어나야 하는 때만 사용하기를 권장함.
    - 위와 같은 특별한 이유가 없을 때는 기본형인 `popBackStack()`를 사용하기를 **권장**.
    
이번 문제는 정확한 타이밍에 **정확히** 백스택이 pop되어야 하는 경우, 사용 환경, 사양 등에 따라 다른 순서로 동작하여 오류가 발생한 경우라고 볼 수 있음.

## 참고 사이트

  - [Android deveoplers의 popBackStack 관련 내용](https://developer.android.com/reference/android/app/FragmentManager#popBackStack())
  - [stackOverflow의 관련 질문](https://stackoverflow.com/questions/44655586/difference-between-popbackstackimmediate-vs-popbackstack)
