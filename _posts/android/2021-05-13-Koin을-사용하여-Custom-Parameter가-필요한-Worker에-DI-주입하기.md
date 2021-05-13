---
categories: 
   - Android
tags: 
   - Kotlin
   - Android
   - WorkManager
   - Koin
---





WorkManager에서 실질적으로 작업을 수행하는 클래스는 Worker이다.

Worker가 필요로 하는 기본 패러미터인  `(context: Context, workerParameters: WorkerParameters)`는 기본으로 제공하는 WorkerFactory가 자동으로 부여할 수 있도록 되어있다.

하지만, Worker가 기본으로 필요로하는 패러미터 외의 값이 추가로 필요한 경우, WorkerFactory를 우리가 직접 커스텀하는 것으로 추가로 할당할 수 있다.

하지만, Koin을 사용하여 module에서 DI를 주입하는 방법으로 Worker에 패러미터를 주입해주는 방법도 있기에, 오늘은 이 방법에 대해 다루어보려고 한다.

만약 WorkManager에 대해 잘 모른다면, [이 포스팅](https://danggai.github.io/android/Android%EC%97%90%EC%84%9C-WorkManager-%EC%82%AC%EC%9A%A9%ED%95%B4%EB%B3%B4%EA%B8%B0/)을 참고하여 간단히 알아볼 수 있다.





## 문제

Worker는 기본적으로 `Worker(context, workerParams)`로, 2가지의 패러미터를 갖는다.

하지만, 프로젝트 진행 중 Worker에서도 Retrofit을 통한 API 통신의 필요성이 나타났다.

이에 Retrofit API를 쉽게 사용할 수 있도록 설계해둔 `apiRepository` 에 Worker에서도 접근할 필요가 생겼다.

Worker의 패러미터를 확장시킨 나만의 Worker를 아래와 같이 구현 후 컴파일하였다.

```kotlin
// RefreshWorker.kt

class RefreshWorker (context: Context, workerParams: WorkerParameters, private val api: ApiRepository) :
    Worker(context, workerParams) {

    override fun doWork(): Result {
        // DO SOMETHING

        return Result.success()
    }
```

당연히 아래와 같은 Exception이 발생한다.



```
2021-05-13 10:01:06.256 7686-7730/danggai.app.parcelwhere E/WM-WorkerFactory: Could not instantiate danggai.app.parcelwhere.worker.RefreshWorker
    java.lang.NoSuchMethodException: danggai.app.parcelwhere.worker.RefreshWorker.<init> [class android.content.Context, class androidx.work.WorkerParameters]
        at java.lang.Class.getConstructor0(Class.java:2332)
        at java.lang.Class.getDeclaredConstructor(Class.java:2170)
        at androidx.work.WorkerFactory.createWorkerWithDefaultFallback(WorkerFactory.java:95)
        at androidx.work.impl.WorkerWrapper.runWorker(WorkerWrapper.java:244)
        at androidx.work.impl.WorkerWrapper.run(WorkerWrapper.java:136)
        at androidx.work.impl.utils.SerialExecutor$Task.run(SerialExecutor.java:91)
        at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1167)
        at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:641)
        at java.lang.Thread.run(Thread.java:919)
2021-05-13 10:01:06.256 7686-7730/danggai.app.parcelwhere E/WM-WorkerWrapper: Could not create Worker danggai.app.parcelwhere.worker.RefreshWorker
```

대충 `apiRepository`에 어떤 패러미터 값을 매칭해줘야 하는지 알 수 없다는 에러.



이는, 앱이 실행될 때 까지는 문제가 없지만, Worker을 인스턴스화 할 수 있는 올바른 초기화 방법이 존재하지 않기 때문에 이런 에러가 발생한다.

이런 문제를 해결하기 위해 우리는 WorkerFactory를 커스텀하는 방법도 있으나, Koin을 사용하여 DI를 주입해주는 것으로 조금 더 간단하게 문제를 해결할 수 있다.

Koin으로 WorkManager에 DI를 주입하는 과정은 아래와 같다.





---

## 1. koin-androidx-workmanager 라이브러리 추가

```groovy
// build.gradle (:app)

def koin_version = "2.2.2"
implementation "org.koin:koin-androidx-workmanager:$koin_version"
```

포스팅 작성중은 2021년 5월 13일 기준, 2.2.2 버전이 가장 최신 버전이다.

기본 Koin 사용을 위한 라이브러리 말고, `koin-androidx-workmanager`를 추가로 implement 해주어야 workManager과 관련된 DI주입이 가능하다.

이는 Koin 2.2 릴리즈, 3.0.0 알파 릴리즈버전부터 지원한다.



[commit 36aeeb5](https://github.com/danggai/FindMyPackage/commit/36aeeb5e81bf8033175ebe9f33892283f73d88fe)

---

## 2. 기본 WorkerManagerInitializer 미사용 설정

```xml
<!-- AndroidManifest.xml -->

<application>
        
    <provider
        android:name="androidx.work.impl.WorkManagerInitializer"
        android:authorities="danggai.app.parcelwhere.App.workmanager-init" 
        tools:node="remove"/>
    
    
    ...
    
</application>
```



`<Provider ... />`을 추가하는 것으로 더 이상 WorkManger 라이브러리가 기본으로 제공하는 초기화를 수행하지 않도록 노드를 제거해줘야 한다.



[commit 5cdec73](https://github.com/danggai/FindMyPackage/commit/5cdec73889f4c6819b98e326d11ce0481a6d0891)

---

## 3. WorkManagerModule 생성하기



```kotlin
// RepositoryModule.kt
val repositoryModule = module {
    single(createdAtStart = false) { ApiRepository(get<Retrofit>().create(ApiInterface::class.java)) }
}

// WorkerFactoryModule.kt
val WorkerFactoryModule = module {
    worker { params -> RefreshWorker(get(), workerParams = params.get(), get()) }
}
```

`RepositoryModule`에서 이미 `ApiRepository`를 싱글튼으로 선언해두었으므로, `WorkerFactoryModule`에서 `get()`만 해도 알아서 Koin에서 매칭해준다.

`WorkManagerModule`에 다른 구현된 Worker들도 선언해주는 것으로 DI를 주입하여 사용할 수 있다.





[ApiRepository.kt의 Raw Code](https://github.com/danggai/FindMyPackage/blob/dev/app/src/main/java/danggai/app/parcelwhere/data/api/ApiRepository.kt)

[commit 5cdec73](https://github.com/danggai/FindMyPackage/commit/5cdec73889f4c6819b98e326d11ce0481a6d0891)

---

## 4. App.kt에서 의존성 주입하기

```kotlin
class App: Application() {

    override fun onCreate() {
        super.onCreate()

        startKoin {
            workManagerFactory()
            modules(listOf(/*another modules*/, WorkerFactoryModule))
        }
    }    
    ...
}

```



두 줄만 추가하면 끝.

[commit 5cdec73](https://github.com/danggai/FindMyPackage/commit/5cdec73889f4c6819b98e326d11ce0481a6d0891)





---

## 사용하기

기존에 WorkManager을 사용하던 것처럼, `WorkRequest`에 `Worker`을 담아 `WorkManager`에 enqueue하는 것으로 사용 가능하다.

원하는 패러미터는 `WorkerFactoryModule.kt`에 추가로 등록해주는 것으로 사용할 수 있다.

이제 백그라운드에서 원하는 Service나 Repository에 쉽게 접근할 수 있으니, 생각했던 대로의 Worker을 이용한 백그라운드 개발을 이어서 진행하면 된다.



끝!







## 참고

[Koin Developers - What's next with Koin? — 2.2 & 3.0 releases](https://medium.com/koin-developers/whats-next-with-koin-2-2-3-0-releases-6c5464ae5e3d)

[Android Developers - 맞춤 WorkManager 구성 및 초기화](https://developer.android.com/topic/libraries/architecture/workmanager/advanced/custom-configuration?hl=ko#remove-default)