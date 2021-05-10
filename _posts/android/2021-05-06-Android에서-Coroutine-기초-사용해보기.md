---
categories: 
   - Android
tags: 
   - Kotlin
   - Android
   - Coroutine
---









기존의 비동기처리는 rx로 진행하고 있었으나 높은 러닝커브로 인해, 기존에 사용하던 방식 외에 다른 방식으로의 응용이 어려워 코루틴을 사용하는 비동기처리도 찍어먹어보려고 한다.



## Coroutine의 장점

[안드로이드 디벨로퍼즈 Coroutine 페이지](https://developer.android.com/kotlin/coroutines?hl=ko)에서는 Coroutine의 장점을 아래와 같이 꼽았다.

- 경량
- 메모리 누수 감소
- 기본으로 제공되는 취소 지원
- Jetpack 통합





## 초기 세팅

Coroutine는 Kotlin 1.3 버전에 추가되었기에 Jetpack에서 기본적으로 Coroutine를 사용할 수 있도록 지원한다.

또한, MVVM 패턴을 지원하기 위해 [lifecycle-viewmodel-ktx](https://developer.android.com/kotlin/ktx?hl=ko#viewmodel) 라이브러리 사용을 권장한다.



앱 생성 시 MVVM 패턴을 선택했다면 기본적으로 implement 되어있지만, 그게 아니라면 해당 라이브러리를 `build.gradle`에 추가하는 것으로 사용할 수 있다.





## 기본적인 비동기 처리 예시 

내가 원했던 비동기처리는 아래와 같다.

1. `ViewModel Init1` 작업은 개별적으로 작동한다.
2. `ViewModel Init2`이 종료된 후, `ViewModel Init3`을 진행한다.
3. 모든 작업이 종료되면 완료되었다는 로그를 남긴다.



위 작업을 수행하기 위해 아래와 같이 ViewModel을 작성하고, 테스트했다.

```kotlin
class MainViewModel : ViewModel() {

    var lvToastMsg: MutableLiveData<String> = MutableLiveData("")

    init {
        viewModelScope.launch {
            init()
        }
    }

    private suspend fun init() = coroutineScope {
        Log.e("init", "Start")

        val deferredOne = async {
            launchAll("ViewModel Init1").await()
            true
        }
        val deferredTwo = async {
            launchAll("ViewModel Init2").await()
            launchAll("ViewModel Init3").await()
            true
        }

        if (deferredOne.await() && deferredTwo.await()) Log.e("init", "Done")
        else Log.e("init", "Failed")
    }
    
    private suspend fun launchAll(tag: String) =
            viewModelScope.async {
                Log.e(tag, "Start")
                launchA()
                launchB()
                launchC()
                lvToastMsg.value = "coroutine \"" + tag + "\" Done"
                Log.e(tag, "Done")
            }

    private suspend fun launchA() {
        Log.e("A", "Start")
        delay(500L)
        Log.e("A", "Complete")
    }

    private suspend fun launchB() {
        Log.e("B", "Start")
        delay(500L)
        Log.e("B", "Complete")
    }

    private suspend fun launchC() {
        Log.e("C", "Start")
        delay(500L)
        Log.e("C", "Complete")
    }
}
```

- `lvToastMsg` 는 Fragment에서 Observe해둔 라이브데이터로, 값이 변하면 토스트 메시지를 출력하도록 만들어둔 변수이다.



작동 결과는 아래와 같다.



```
2021-05-06 15:40:13.519 13796-13796 E/init: Start
2021-05-06 15:40:13.519 13796-13796 E/ViewModel Init1: Start
2021-05-06 15:40:13.519 13796-13796 E/A: Start
2021-05-06 15:40:13.520 13796-13796 E/ViewModel Init2: Start
2021-05-06 15:40:13.520 13796-13796 E/A: Start
2021-05-06 15:40:14.021 13796-13796 E/A: Complete
2021-05-06 15:40:14.021 13796-13796 E/B: Start
2021-05-06 15:40:14.022 13796-13796 E/A: Complete
2021-05-06 15:40:14.022 13796-13796 E/B: Start
2021-05-06 15:40:14.523 13796-13796 E/B: Complete
2021-05-06 15:40:14.524 13796-13796 E/C: Start
2021-05-06 15:40:14.525 13796-13796 E/B: Complete
2021-05-06 15:40:14.526 13796-13796 E/C: Start
2021-05-06 15:40:15.027 13796-13796 E/C: Complete
2021-05-06 15:40:15.081 13796-13796 E/ViewModel Init1: Done
2021-05-06 15:40:15.084 13796-13796 E/C: Complete
2021-05-06 15:40:15.100 13796-13796 E/ViewModel Init2: Done
2021-05-06 15:40:15.101 13796-13796 E/ViewModel Init3: Start
2021-05-06 15:40:15.101 13796-13796 E/A: Start
2021-05-06 15:40:15.606 13796-13796 E/A: Complete
2021-05-06 15:40:15.606 13796-13796 E/B: Start
2021-05-06 15:40:16.109 13796-13796 E/B: Complete
2021-05-06 15:40:16.109 13796-13796 E/C: Start
2021-05-06 15:40:16.612 13796-13796 E/C: Complete
2021-05-06 15:40:16.649 13796-13796 E/ViewModel Init3: Done
2021-05-06 15:40:16.650 13796-13796 E/init: Done
```



위 실행 결과로 아래와 같은 정보를 배울 수 있다.

- 별개의 Scope (이 경우, `viewModelScope`)에서 이루어지는 `.await()` 동작은 **동시에 진행**된다.
- 한 개의 Scope 내에서 `.await()` 동작은 **먼저 동작이 끝난 후 이어서 진행 **(비동기적으로)된다.





위 예제에서는 `launch`와 `async` 동작을 혼재하여 사용하였지만, 아래와 같은 차이가 있다.

- `launch`: 새 코루틴을 시작하고, 결과를 반환하지 않음. '실행 후 삭제'로 간주되는 모든 작업에 사용을 권장.
- `async`: 새 코루틴을 시작하고, 결과를 반환함. **`suspend fun` 에서만 호출 가능.** 

또한 두 동작은 예외를 서로 다르게 처리하나, 자세한 내용은 [코루틴의 취소 및 예외](https://medium.com/androiddevelopers/cancellation-in-coroutines-aa6b90163629)를 참조.





## ViewModelScope? CoroutineScope? withContext?

세 동작은 모두 유사해보인다.

세 동작 모두 비동기처리를 지원하는데 사용되고 평소에는 어떤걸 골라 사용하던지 작동에 문제가 크게 되지는 않는다.

하지만 세 가지 동작에 대해 조금 더 자세히 말하자면 아래와 같다.



---

- `viewModelScope`는 `CoroutineScope`를 ViewModel의 생명주기에 맞추어 간편히 사용할 수 있도록 만든 `CoroutineScope`의 한 유형이다.
  - `viewModelScope`는 `CoroutineScope(SupervisorJob() + Dispatchers.Main.immediate)`와 유사하게 동작한다.
  - 아래 두 동작은 일반적으로 동일하게 작동하지만, ViewModel에서는 편의성과 leak에서 안전하기 위해 위의 동작을 사용하기를 추천한다.

```kotlin
   fun launchAllWithViewModelScope() {
        viewModelScope.launch {
            launchAll("in viewModel Scope")
        }
    }

    private suspend fun launchAll(tag: String) =
            withContext(viewModelScope.coroutineContext) {
                Log.e(tag, "Start")
                launchA()
                launchB()
                launchC()
                lvToastMsg.value = "coroutine \"" + tag + "\" Done"
                Log.e(tag, "Done")
            }
```

```kotlin
    fun launchAllWithViewModelScope() {
        CoroutineScope(SupervisorJob() + Dispatchers.Main).launch {
            launchAll("in viewModel Scope")
        }
    }
    
    private suspend fun launchAll(tag: String) =
            withContext(viewModelScope.coroutineContext) {
                /* 위와 동일 */
            }
```





---

- `CoroutineScope`는 `withContext`의 한 유형이다.
  - `CoroutineScope`와 `withContext(this.coroutineContext)`는 같은 의미를 갖는다고 볼 수 있다.
  - `withContext`는 현재 작동 중인 `Coroutine` 동작을 **일시 중지**시키고 동작한다.
  - `CoroutineScope`는 `Dispatcher`을 지정할 수 없으며, 무조건 자신을 실행시킨 `context`를 통해 작동한다.
  - 아래 두 동작은 일반적으로 동일하게 작동하지만, 안드로이드 스튜디오는 윗 동작을 추천한다.

```kotlin
 private suspend fun init() = coroutineScope {
        Log.e("init", "Start")

        val deferredOne = async {
            launchAll("ViewModel Init1")
            true
        }
        val deferredTwo = async {
            launchAll("ViewModel Init2")
            launchAll("ViewModel Init3")
            true
        }

        if (deferredOne.await() && deferredTwo.await()) Log.e("init", "Done")
        else Log.e("init", "Failed")
    }

    private suspend fun launchAll(tag: String) =
            withContext(viewModelScope.coroutineContext) {
                Log.e(tag, "Start")
                launchA()
                launchB()
                launchC()
                lvToastMsg.value = "coroutine \"" + tag + "\" Done"
                Log.e(tag, "Done")
            }
```

```kotlin
  private suspend fun init() = coroutineScope {
        Log.e("init", "Start")

        val deferredOne = async {
            launchAll("ViewModel Init1").await()
            true
        }
        val deferredTwo = async {
            launchAll("ViewModel Init2").await()
            launchAll("ViewModel Init3").await()
            true
        }

        if (deferredOne.await() && deferredTwo.await()) Log.e("init", "Done")
        else Log.e("init", "Failed")
    }
    
    private suspend fun launchAll(tag: String) =
            viewModelScope.async {
                Log.e(tag, "Start")
                launchA()
                launchB()
                launchC()
                lvToastMsg.value = "coroutine \"" + tag + "\" Done"
                Log.e(tag, "Done")
            }
```



---

- 아래 세 가지 비동기 Coroutine는 모두 같은 Thread에서 작동한다.

```kotlin
    private suspend fun launchAll(tag: String) =
            withContext(viewModelScope.coroutineContext) {
                Log.e(tag, "Start")
                launchA()
                launchB()
                launchC()
                lvToastMsg.value = "coroutine \"" + tag + "\" Done"
                Log.e(tag, "Done")
            }

    private suspend fun launchAll2(tag: String) =
            coroutineScope {
                Log.e(tag, "Start")
                launchA()
                launchB()
                launchC()
                lvToastMsg.value = "coroutine \"" + tag + "\" Done"
                Log.e(tag, "Done")
            }

    private suspend fun launchAll3(tag: String) {
        Log.e(tag, "Start")
        launchA()
        launchB()
        launchC()
        lvToastMsg.value = "coroutine \"" + tag + "\" Done"
        Log.e(tag, "Done")
    }
```

위 세 동작 모두 Coroutine 실행 중 ViewModel 종료 시, 정상적으로 작동이 종료된다.



`launchAll2()`가 가장 활용도가 떨어지는 방식으로 보인다.

`launchAll3()`만큼 간결하지도 않으며, `launchAll()`처럼 다른 `Dispatchers`에 접근 할 수도 없다.





---

- 비동기 Coroutine 블럭을 생성 하는 함수는, `Scope`를 명시하여야 함.

```kotlin
private suspend fun init() = viewModelScope.launch {  // 가능
    Log.e("init", "Start")

    val deferredOne = async {			 // 새로운 코루틴 블럭
        launchAll("ViewModel Init1")
        true
    }
    val deferredTwo = async {			 // 새로운 코루틴 블럭
        launchAll("ViewModel Init2-1")
        launchAll("ViewModel Init2-2")
        true
    }

    if (deferredOne.await() && deferredTwo.await()) Log.e("init", "Done")
    else Log.e("init", "Failed")
}
```

```kotlin
private suspend fun init() {  			// 불가능
    Log.e("init", "Start")

    val deferredOne = async {			 // 새로운 코루틴 블럭
        launchAll("ViewModel Init1")
        true
    }
    val deferredTwo = async {			 // 새로운 코루틴 블럭
        launchAll("ViewModel Init2-1")
        launchAll("ViewModel Init2-2")
        true
    }

    if (deferredOne.await() && deferredTwo.await()) Log.e("init", "Done")
    else Log.e("init", "Failed")
}
```







## 정리

위에서 공부한 내용들을 정리하자면 아래와 같다.



1. **비동기 Coroutine 호출**을 위해 사용하는 Coroutine 동작은 아래 두 경우로 나눠 호출하자.
   1. ViewModel 에서는 `viewModelScope`을 통해 호출하자.
      - Main Thread 외의 Thread에 접근 할 때는, Coroutine 내에서 `withContext(Dispatchers){...}`를 통해 처리.
   2. 그 외 동작에서는 `CoroutineScope`를 사용해서 작동시키자.
2. **비동기 Coroutine 선언** 시에는 아래 두 경우로 나눠 호출하자.
   1. 일반적인 경우에는 `suspend fun launchAll {}`를 통해 선언하고, `async{...}`블록에서 호출하는 것이 직관적이다.
   2. 굳이 다른 context에서 선언 해야 한다면, `withContext`를 통해 선언하자.
3. **새로운 Coroutine 블럭을 호출 시**에는, 호출하는 함수에 `Scope`를 명시하여야 함. 





## 참고

- [Coroutine - withContext 와 coroutineScope 의 비교](https://sandn.tistory.com/99)
- [[Android, MVVM, Coroutine] 활용 #3 - ViewModel, LiveData의 Coroutine](https://tourspace.tistory.com/343)





-