---
categories: 
   - Android
tags: 
   - Kotlin
   - Android
   - Rx
---





 













![Buffer, skip](https://github.com/donggi9313/donggi9313.github.io/blob/master/assets/image/20210402/00.png?raw=true)



흔히 앱을 사용하다 보면 최상위 Activity에서 백버튼을 누르면 바로 종료 되는 앱도 있고, '한번 더 누르면 종료됩니다.' 라는 토스트 메시지를 보여주는 앱도 있다.

한 번 정도의 백버튼 클릭은 실수로 할 수 있기에, 완충제 역할을 해주어 사용자의 경험을 개선시키는 고전적이지만, 효율적인 UX 개선안이다.





## 기존 방법

BackButton을 누르면, `onBackPressed()` 함수가 호출된다.

종료를 방지하고 싶은 Activity에서 해당 함수를 override 하여 동작을 처리하는 방식으로 우리는 구현해왔다.

기존 코드의 예시는 아래와 같다.

```kotlin
val BACK_BUTTON_INTERVAL: Long = 1000
var recentBackButtonTime: Long = 0
```

```kotlin
override fun onBackPressed() {
    val currentBackButtonTime = System.currentTimeMillis()
    if (currentBackButtonTime - recentBackButtonTime in 0..BACK_BUTTON_INTERVAL) {
        super.onBackPressed()
    } else {
        recentBackButtonTime = currentBackButtonTime
        Toast.makeText(applicationContext, "한번 더 눌러서 종료", Toast.LENGTH_SHORT).show()
    }
}
```



최근 백버튼 클릭 시간을 기록하고, 두 시간 사이 간격이 1초 미만인 경우, `super.onBackPressed()`를 호출하도록 구현되어있다.

최상위 액티비티라면, 앱이 종료 될 것이다.





## Rx로 개선 하는 방법

Rx를 사용하여 BackButton Event를 구현하기 위해서 극복해야 하는 아래와 같은 문제점이 2가지 정도 있다.





### BackButton을 누른 시간 1번은 현재, 기존 시간으로 총 2번 전달되어야 함.

한번 백버튼을 누른 시간은 아래와 같이 총 2번 비교에 사용된다.

1. 이전에 누른 시간과의 interval 비교
2. 이 다음 누를 시간과의 interval 비교



이 구현을 위해 위처럼 `recentBackButtonTime`을 사용 하는 것도 방법이기는 하다.

하지만 조금 더 똑똑하게 구현하기 위해 Buffer이라는 개념을 사용하고자 한다. 이에 대한 자세한 내용은 [공식 문서](http://reactivex.io/documentation/ko/operators/buffer.html)를 참고하는 것도 좋을 것 같다.



![Buffer, skip](https://github.com/donggi9313/donggi9313.github.io/blob/master/assets/image/20210402/01.png?raw=true)

나는 이 중에서 `Buffer(count, skip)` 을 사용하여 구현하려고 한다.

위 2가지 패러미터는 대략 아래와 같은 의미를 지닌다.

1. `count` : count 개 만큼 값을 모으면 observable을 발행함.
2. `skip` : skip개 마다 새로운 observable를 생성하여 값을 수집함.



![Buffer, skip](https://github.com/donggi9313/donggi9313.github.io/blob/master/assets/image/20210402/02.png?raw=true)

`Buffer(4,2)`를 예로 들면 이런 느낌이다.



이 문제는 `Buffer(2,1)`을 사용해 해결할 수 있었다.





### 첫 BackButton 부터 toast를 출력하도록

`PublishSubject`를 사용하여 구현 하는 경우, 첫 BackButton 클릭 시엔 아무 이벤트도 발생하지 않는다.

첫 이벤트 발생 전에도 기본 값이 필요하므로, `BehaviorSubject`를 사용하여 기본 값을 할당하는 것으로 해결 할 수 있다.





## 개선 된 코드

```kotlin
private val rxBackButtonAction: Subject<Long> = BehaviorSubject.createDefault(0L).toSerialized()
val BACK_BUTTON_INTERVAL: Long = 1000
```

```kotlin
override fun onBackPressed() {
    rxBackButtonAction.onNext(System.currentTimeMillis())
}

private fun initRx() {
    rxBackButtonAction	
        .observeOn(AndroidSchedulers.mainThread())
        .buffer(2,1)
        .map { it[1] - it[0] < BACK_BUTTON_INTERVAL }
        .subscribe {
            if (it) { super.onBackPressed() } else { Toast.makeText(applicationContext, "한번 더 눌러서 종료", Toast.LENGTH_SHORT).show() }
        }.addDisposableExt()
}
```



