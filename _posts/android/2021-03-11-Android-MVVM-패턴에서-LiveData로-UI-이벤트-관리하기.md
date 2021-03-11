

# 개요

안드로이드 앱 개발 중, MVVM 패턴에서 LiveData를 사용하여 UI 이벤트를 관리하기 위해서 기존에 아래와 같은 데이터 흐름 방식을 따라갔었다.



1. ViewModel에서 변수 선언.
2. 해당 변수를 Fragment에서 Observe.
3. UI 이벤트 발생 시, ViewModel에서 해당 변수 값 변화.
4. Fragment에서 Observe 했으므로 activity 실행 됨.
5. **만료된 observe 해제.**



이 중 5번에 대한 처리를 하지 않아, Orientation이 변경되는 등의 액션이 발생하면 계속해서 이벤트가 발생했다.



기존 코드는 아래와 같은 흐름을 따라가고 있었다.





# 기존 코드

ViewModel.kt

```kotlin
var lvStartAddAct: MutableLiveData<Boolean> = MutableLiveData(false)

...

fun openNewActivity(view: View) {
	lvStartAddAct.value = true
}
```



Fragment.kt

```kotlin
mVM.lvStartAddAct.observe(viewLifecycleOwner, Observer{
	// Start Activity
})
```





이런 방식을 따라갔기에, 화면이 재설계 될 때 마다 UI 이벤트가 반복적으로 발생하였다.



이에 아래 Event 클래스를 통해 단발적으로만 호출되도록 변경해줄 수 있다.





#  Event.kt 

Event.kt

```kotlin
open class Event<out T>(private val content: T) {
    var hasBeenHandled = false
        private set 
    
    fun getContentIfNotHandled(): T? {
        return if (hasBeenHandled) {
            null
        } else {
            hasBeenHandled = true
            content
        }
    }

    fun peekContent(): T = content
}
```



EventObserer.kt

```kotlin
class EventObserver<T>(private val onEventUnhandledContent: (T) -> Unit) : Observer<Event<T>> {
    override fun onChanged(event: Event<T>?) {
        event?.getContentIfNotHandled()?.let { value ->
            onEventUnhandledContent(value)
        }
    }
}
```



이 두 클래스를 생성 후, 아래처럼 코드를 변경해주면 Observe를 해제하지 않고도 단발적으로 UI 이벤트를 발생시킬 수 있다.





# 변경된 코드

ViewModel.kt

```kotlin
var lvStartAddAct = MutableLiveData<Event<Boolean>>()

fun openNewActivity(view: View) {
	lvStartAddAct.value = Event(true)
}
```



Fragment.kt

```kotlin
mVM.lvStartAddAct.observe(viewLifecycleOwner, EventObserver {
	// Start Activity
})
```



PublishSubject나 다른 방법을 통해서 UI 이벤트를 처리할 수도 있지만, 이 방법을 통해 처리를 하면 아래와 같은 장점이 있다.



1. Livedata의 형태로 취급 가능.
2. 상대적으로 러닝커브가 가파른 Rx를 사용하지 않고도 관리 가능.
3. UI 이벤트의 특성 상 반복적으로 자주 사용될 수 밖에 없는 코드를 짧고 간결하게 사용 가능 -> 유지보수에 용이







# 참고자료

[Event.kt 참고 자료](https://gist.github.com/JoseAlcerreca/5b661f1800e1e654f07cc54fe87441af)