---
categories: 
   - Android
tags: 
   - Kotlin
   - Android
   - WorkManager

---





## WorkManager이 뭐야?

안드로이드 앱 개발 시 백그라운드 작업을 쉽게 예약/실행 할 수 있게 해주는 Google에서 발표한 라이브러리이다.

앱의 종료나 기기의 재시작에도 예약 된 백그라운드 작업을 사용할 수 있도록 알아서 처리해줌!







## 왜 WorkManager을 사용해야해?

우리는 안드로이드에서 기존도 다양하고 많은 Background Tasking을 수행하기 위해 넓고 많은 개념을 알아야했다.

어떤 API 버전에서는 어떤 기술로 백그라운드에서 동작하게 한다던가, 어떤 기술은 어느 버전 API는 지원하지 않는다거나.





WorkManager는 Scheduler, JobDispatcher, AlarmManager, Service 등등의 복잡한 Background Tasking을 위한 테크닉을 버전에 맞게 사용할 수 있도록 구글에서 잘 정리해둔 기술이다.

더 이상 개발자가 어떤 버전에서 어떤 테크닉을 사용할지 고려하는데에 자원을 낭비하지 않아도 된다. (아예 몰라도 된다는 뜻은 아니다!)







## 기본적인 4가지 개념



- Worker: 작업 내용이 담길 클래스.
  - `doWork()`라는 함수를 구현하는 것으로, background Thread에서 작업하고 싶은 코드를 담음.
  - 우리가 주로 구현해야 하는 코드는 이 곳에 작성하게 될 것.





- WorkRequest: 작업 환경 및 조건을 명시할 클래스.
  - 각각의 task를 의미함.
  - Worker을 작동시키기 위한 조건과 환경 등을 명시하여 WorkManager에 Enqueue 시키면 명시된 내용대로 Worker이 작동 됨.





- WorkManager: 전체 작업이 담기는 관리 클래스.
  - 처리해야 하는 작업들을 자신의 Queue에 넣고 관리함.
  - 싱글튼으로 구현되어있어 `getInstance(context)`를 통해 인스턴스를 받아 사용함.






- WorkStatus: WorkRequest의 현재 상태 및 상세한 정보를 담고있음.
  - WorkRequest의 고유한 ID, 현재 Status 등의 정보를 담고있음.
  - ID를 통해 특정 Request를 중지시키거나, LiveData인 Status를 Observe하는 것으로도 활용 가능. 





## gradle에 implement하기

```groovy
def workmanager_version = "2.5.0"
implementation "androidx.work:work-runtime-ktx:$workmanager_version"
```

Kotlin + Coroutine로 구성 된 WorkManger을 사용하기 위해서 App 레벨의 build.gradle에 위 코드를 추가해주자.



이 글이 작성되는 시기에는 2.5.0버전이 최신 버전이였다.

지금도 활발히 업데이트 되는 라이브러리이므로, 항상 [WorkManager 안드로이드 디벨로퍼](https://developer.android.com/topic/libraries/architecture/workmanager/basics?hl=ko) 사이트를 참고하는 습관을 들이자.



혹시 자바만 사용하는 경우나, rx를 함께 사용하고 싶은 경우라면 아래 코드를 추가해주자.

```groovy
def work_version = "2.5.0"

// (Java only)
implementation "androidx.work:work-runtime:$work_version"
    
    
// optional - RxJava2 support
implementation "androidx.work:work-rxjava2:$work_version"
```





## 기초적인 WorkManager 사용 방법



MVVM 패턴에서는 아래와 같은 방식으로 사용 가능하다.



---

### WorkManager

```kotlin
// MainFragment.kt

val workManager = WorkManager.getInstance(context)
```

WorkManager의 인스턴스를 받아오기 위해 context가 필요하므로, Fragment에서 ViewModel을 선언하듯이 선언했다.



---

### WorkRequest

```kotlin
// MainViewModel.kt

val workRequest = OneTimeWorkRequestBuilder<MyWorker>().build()
workManager.enqueue(workRequest)
```

```kotlin
// MainViewModel.kt

val workRequest = PeriodicWorkRequestBuilder<MyWorker>(period, TimeUnit.MINUTES).build()
workManager.enqueue(workRequest)
```



- `OneTimeWorkRequestBuilder() `
  - enqueue 되는 순간, 단 한 번 work를 바로 수행한다.
- `PeriodicWorkRequestBuilder(repeatInterval: Long, repeatIntervalTimeUnit: TimeUnit)`
  - enqueue 되는 순간 work를 수행하며, `repeatInterval` 마다 작업을 반복적으로 수행한다.
  - 간격을 아무리 짧게 설정해도, 15분이내에 작업을 수행하지 않는다.



---

### Worker

```kotlin
// MyWorker.kt

class MyWorker(context: Context, workerParams: WorkerParameters) :
    Worker(context, workerParams) {
    override fun doWork(): Result {
        /* 처리해야할 작업에 관한 코드들 */
        Log.e("MyWorker", "Hello!")
        return Result.success()
    }
}
```

- Worker은 반드시 `doWork()`을 override 해야한다.



```kotlin
// MyCoroutineWorker.kt

class MyCoroutineWorker(context: Context, workerParams: WorkerParameters) :
    CoroutineWorker(context, workerParams) {

    override suspend fun doWork(): Result {
        /* 처리해야할 작업에 관한 코드들 */
        delay(500L)
        Log.e("MyCoroutineWorker", "Hello!")
        return Result.success()
    }
}
```

- CoroutineWorker는 반드시 `doWork()`이 `suspend fun`  으로 선언되어있다.
- 또한 Worker에는 `public`으로 선언되어 있는 `onStopped()`이 `final`로 선언되어있어, override 할 수 없다.



---

### ViewModel에서 WorkRequest해보기

```kotlin
// MainViewModel.kt

class MainViewModel : ViewModel() {
    fun startWorkRequests(workManager: WorkManager) {
        workManager.cancelAllWork()
        
        OneTimeWorkRequest(workManager)
        PeriodicWorkRequest(15, workManager)

        OneTimeCoroutineWorkRequest(workManager)
        PeriodicCoroutineWorkRequest(15, workManager)
    }

    fun OneTimeWorkRequest(workManager: WorkManager) {
        /*단발 WorkRequest*/
        val workRequest = OneTimeWorkRequestBuilder<MyWorker>().build()
        workManager.enqueue(workRequest)
    }

    fun PeriodicWorkRequest(period: Long, workManager: WorkManager) {
        /*주기적으로 반복하는 WorkRequest*/
        val workRequest = PeriodicWorkRequestBuilder<MyWorker>(period, TimeUnit.MINUTES).build()
        workManager.enqueue(workRequest)
    }

    fun OneTimeCoroutineWorkRequest(workManager: WorkManager) {
        /*단발 CoroutineWorkRequest*/
        val workRequest = OneTimeWorkRequestBuilder<MyCoroutineWorker>().build()
        workManager.enqueue(workRequest)
    }

    fun PeriodicCoroutineWorkRequest(period: Long, workManager: WorkManager) {
        /*주기적으로 반복하는 CoroutineWorkRequest*/
        val workRequest = PeriodicWorkRequestBuilder<MyCoroutineWorker>(period, TimeUnit.MINUTES).build()
        workManager.enqueue(workRequest)
    }
}
```



ViewModel에  테스트를 위한 WorkRequest를 요청할 함수를 생성하였다.

Fragment에서 선언 된 `viewModel`에서 해당 함수에 WorkManager을 담아 전달하는 방식을 목표로 구현해보았다.





### 실행 결과

```
2021-05-11 16:17:06.418 14641-14686/com.example.workersample E/MyWorker: Hello!
2021-05-11 16:17:06.420 14641-14687/com.example.workersample E/MyWorker: Hello!
2021-05-11 16:17:06.997 14641-14688/com.example.workersample E/MyCoroutineWorker: Hello!
2021-05-11 16:17:06.997 14641-14690/com.example.workersample E/MyCoroutineWorker: Hello!
```

```
2021-05-11 16:17:17.213 14720-14770/com.example.workersample E/MyWorker: Hello!
2021-05-11 16:17:18.378 14720-14773/com.example.workersample E/MyWorker: Hello!
2021-05-11 16:17:18.363 14720-14767/com.example.workersample E/MyCoroutineWorker: Hello!
2021-05-11 16:17:20.574 14720-14767/com.example.workersample E/MyCoroutineWorker: Hello!
```

```
2021-05-11 16:17:43.255 14879-14922/com.example.workersample E/MyWorker: Hello!
2021-05-11 16:17:43.257 14879-14923/com.example.workersample E/MyWorker: Hello!
2021-05-11 16:17:43.846 14879-14926/com.example.workersample E/MyCoroutineWorker: Hello!
2021-05-11 16:17:43.846 14879-14924/com.example.workersample E/MyCoroutineWorker: Hello!
```

총 3번 동작시켜 보았을 때, 2번째 동작에서만 두 개의 CoroutineWorker이 모두 같은 Thread에서 동작하고 있었다.

Coroutine의 특성 상 병렬 처리가 아닌 **동시 처리**이므로 한 Thread에서 작동하려고 하는 성질이 있는건 아닐까 하는 의심이 들었지만, 이 내용은 나중에 코루틴을 더 깊게 이해하고 나서 다시 공부하는게 나을 것 같다.









## Source Code

https://github.com/danggai/WorkerExercise

이 포스팅 내의 코드는 위 주소에 저장해둔 연습용 프로젝트를 통해 작성 됨.





## 참고

https://dongsik93.github.io/til/2020/05/15/til-jetpack-workmanager/

https://developer.android.com/topic/libraries/architecture/workmanager/advanced/coroutineworker?hl=ko

https://aroundck.tistory.com/7208