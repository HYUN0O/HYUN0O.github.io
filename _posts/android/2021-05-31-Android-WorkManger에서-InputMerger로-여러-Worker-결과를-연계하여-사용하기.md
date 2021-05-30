---
categories: 
   - Android
tags: 
   - Kotlin
   - Android
   - Worker
---





WorkManager을 공부하던 중, InputMerger에 대한 내용이 잘 이해가 되지 않아 추가로 공부해보고, 간단한 예제를 만들어 학습하였다.





## InputMergers

- inputMerger는 동일한 키 값을 갖는 `workDataOf(Pair(key, value))`가 중복되어 인입될 때, 해당 키-값을 어떻게 관리할지 선택하는데 주로 사용된다.



- inputMerger가 필요한 상황은 아래와 같다.
  1. `workManager.beginWith().then().then().enqueue()` 등, OneTImeWorkRequests를 연계하여 사용하는 경우
  2. `beginWith(workerA, workerA, workerA)`처럼 동일한 worker을 복수로 실행하거나 동일한 키 값을 지니는 결과를 여러번 반환할 때



- inputMerger는 `ArrayCreatingInputMerger`, `OverwritingInputMerger`로 총 2가지 중에 선택해서 사용할 수 있다.



- worker의 실행 결과로 넘겨준 `workDataOf(...)`는 다음에 작동하는 worker에서 `inputData.get(type)(key: String)`로 꺼내 쓸 수 있다.
  - 해당 키 값의 데이터가 없을 경우 `null`을 반환함.







## ArrayCreatingInputMerger

동일한 키-값을 갖는 `workDataOf(Pair(key, value))`들을 Array형으로 만든다.

체인되어 작동하는 worker에서 `inputData.getIntArray(key: String)` 형으로 받아 사용할 수 있다.



에제는 주사위를 던지는 `DiceRollWorker`을 총 세 번 작동시켜 값을 더하는 `DiceSumWorker`으로 이루어져있다.





```kotlin
// DiceRollWorker.kt

class DiceRollWorker(context: Context, workerParams: WorkerParameters) :
    Worker(context, workerParams) {
    override fun doWork(): Result {
        Log.d("DiceRollWorker", "start")

        val random = Random()
        val diceResult = 1 + random.nextInt(6)

        Log.e("DiceRollWorker", "Dice roll result = $diceResult!")
        return Result.success(workDataOf(Pair("DiceResult", diceResult)))
    }
}
```



```kotlin
// DiceSumWorker.kt

class DiceSumWorker(context: Context, workerParams: WorkerParameters) :
    Worker(context, workerParams) {
    override fun doWork(): Result {
        Log.d("DiceSumWorker", "start")

        val list = inputData.getIntArray("DiceResult")
        var diceSum = 0

        list?.let {
            Log.d("DiceSumWorker","$list")
            for (int in list) {
                diceSum += int
            }
        }

        Log.e("DiceSumWorker", "Dice sum = $diceSum!")
        return Result.success(workDataOf(Pair("DiceSum", diceSum)))
    }
}
```



```kotlin
// MainViewModel.kt

fun startWorkRequests(workManager: WorkManager) {
    viewModelScope.launch {
        delay(1000L)
        ArrayCreatingInputMerger(workManager)
    }        
}

fun ArrayCreatingInputMerger(workManager: WorkManager) {
    val diceRollWorker1 = OneTimeWorkRequestBuilder<DiceRollWorker>().build()
    val diceRollWorker2 = OneTimeWorkRequestBuilder<DiceRollWorker>().build()
    val diceRollWorker3 = OneTimeWorkRequestBuilder<DiceRollWorker>().build()

    val diceSumWorker = OneTimeWorkRequestBuilder<DiceSumWorker>()
        .setInputMerger(ArrayCreatingInputMerger::class)
        .build()

    workManager.beginWith(listOf(diceRollWorker1, diceRollWorker2, diceRollWorker3))
        .then(diceSumWorker).enqueue()
}
```





### 작동 결과

```
2021-05-18 13:15:41.352 14101-14163/com.example.workersample D/DiceRollWorker: start
2021-05-18 13:15:41.356 14101-14163/com.example.workersample E/DiceRollWorker: Dice roll result = 3!
2021-05-18 13:15:41.367 14101-14164/com.example.workersample D/DiceRollWorker: start
2021-05-18 13:15:41.369 14101-14164/com.example.workersample E/DiceRollWorker: Dice roll result = 5!
2021-05-18 13:15:41.377 14101-14165/com.example.workersample D/DiceRollWorker: start
2021-05-18 13:15:41.378 14101-14165/com.example.workersample E/DiceRollWorker: Dice roll result = 1!
2021-05-18 13:15:41.516 14101-14144/com.example.workersample D/DiceSumWorker: start
2021-05-18 13:15:41.516 14101-14144/com.example.workersample E/DiceSumWorker: Dice sum = 9!
```









## OverwritingInputMerger



동일한 키-값을 갖는 `workDataOf(Pair(key, value))`들을 마지막에 `SUCCESS`된 데이터로 덮어씌운다.

체인되어 작동하는 worker에서 `inputData.getInt(key: String)` 형으로 받아 사용할 수 있다.



에제는 주사위를 던지는 `DiceRollWorker`을 총 세 번 작동시켜 마지막에 종료된 주사위를 던진 시간을 확인하는 `DiceRolledTimeWorker`으로 이루어져있다.



참고로, worker의 작동 시간에 따라 마지막에 `SUCCESS`된 값으로 덮어씌우므로, `workManager.beginWith(listOf(WorkerA, WorkerB, WorkerC))`순으로 실행하여도, `WorkerC`의 결과로 덮어씌워진다는 보장은 없다.



```kotlin
// DiceRollWorker.kt

class DiceRollWorker(context: Context, workerParams: WorkerParameters) :
    Worker(context, workerParams) {
    override fun doWork(): Result {
        Log.d("DiceRollWorker", "start")

        val random = Random()
        val diceResult = 1 + random.nextInt(6)
        val time = SimpleDateFormat("yyyy-MM-dd'T'HH:mm:ss.SSS").format(Date()).toString()

        Log.e("DiceRollWorker", "Dice roll result = $diceResult!")
        Log.e("DiceRollWorker", "Dice rolled time = $time!")
        return Result.success(workDataOf(Pair("DiceResult", diceResult), Pair("RolledTime", time)))
    }
}
```



```kotlin
// DiceRolledTimeWorker.kt

class DiceRolledTimeWorker(context: Context, workerParams: WorkerParameters) :
    Worker(context, workerParams) {
    override fun doWork(): Result {
        Log.d("DiceRolledTimeWorker", "start")
        val rolledTime = inputData.getString("RolledTime")

        Log.e("DiceRolledTimeWorker", "last Rolled Time = $rolledTime!")
        return Result.success(workDataOf(Pair("RolledTime", rolledTime)))
    }
}
```



```kotlin
// MainViewModel.kt

fun startWorkRequests(workManager: WorkManager) {
    viewModelScope.launch {
        delay(1000L)
        ArrayCreatingInputMerger(workManager)
    }        
}

fun OverwritingInputMerger(workManager: WorkManager) {
    val diceRollWorker1 = OneTimeWorkRequestBuilder<DiceRollWorker>().build()
    val diceRollWorker2 = OneTimeWorkRequestBuilder<DiceRollWorker>().build()
    val diceRollWorker3 = OneTimeWorkRequestBuilder<DiceRollWorker>().build()

    val rolledTimeWorker = OneTimeWorkRequestBuilder<DiceRolledTimeWorker>()
        .setInputMerger(OverwritingInputMerger::class)
        .build()

    workManager.beginWith(listOf(diceRollWorker1, diceRollWorker2, diceRollWorker3))
        .then(rolledTimeWorker).enqueue()
}
```



### 작동 결과

```
2021-05-18 13:15:42.266 14101-14163/com.example.workersample E/DiceRollWorker: Dice roll result = 5!
2021-05-18 13:15:42.266 14101-14163/com.example.workersample E/DiceRollWorker: Dice rolled time = 2021-05-18T13:15:42.265!
2021-05-18 13:15:42.271 14101-14164/com.example.workersample D/DiceRollWorker: start
2021-05-18 13:15:42.272 14101-14164/com.example.workersample E/DiceRollWorker: Dice roll result = 3!
2021-05-18 13:15:42.272 14101-14164/com.example.workersample E/DiceRollWorker: Dice rolled time = 2021-05-18T13:15:42.271!
2021-05-18 13:15:42.277 14101-14165/com.example.workersample D/DiceRollWorker: start
2021-05-18 13:15:42.278 14101-14165/com.example.workersample E/DiceRollWorker: Dice roll result = 2!
2021-05-18 13:15:42.278 14101-14165/com.example.workersample E/DiceRollWorker: Dice rolled time = 2021-05-18T13:15:42.277!
2021-05-18 13:15:42.428 14101-14144/com.example.workersample D/DiceRolledTimeWorker: start
2021-05-18 13:15:42.428 14101-14144/com.example.workersample E/DiceRolledTimeWorker: last Rolled Time = 2021-05-18T13:15:42.277!
```

```
2021-05-18 15:20:32.095 18096-18156/com.example.workersample E/DiceRollWorker: Dice roll result = 5!
2021-05-18 15:20:32.095 18096-18156/com.example.workersample E/DiceRollWorker: Dice rolled time = 2021-05-18T15:20:32.095!
2021-05-18 15:20:32.098 18096-18158/com.example.workersample D/DiceRollWorker: start
2021-05-18 15:20:32.099 18096-18158/com.example.workersample E/DiceRollWorker: Dice roll result = 5!
2021-05-18 15:20:32.099 18096-18158/com.example.workersample E/DiceRollWorker: Dice rolled time = 2021-05-18T15:20:32.098!
2021-05-18 15:20:32.102 18096-18157/com.example.workersample D/DiceRollWorker: start
2021-05-18 15:20:32.102 18096-18157/com.example.workersample E/DiceRollWorker: Dice roll result = 5!
2021-05-18 15:20:32.102 18096-18157/com.example.workersample E/DiceRollWorker: Dice rolled time = 2021-05-18T15:20:32.102!
2021-05-18 15:20:32.157 18096-18138/com.example.workersample D/DiceRolledTimeWorker: start
2021-05-18 15:20:32.157 18096-18138/com.example.workersample E/DiceRolledTimeWorker: last Rolled Time = 2021-05-18T15:20:32.095!
```



> 처음 실행한 Worker의 결과가 덮어씌워지기도 한다.







-

## 참고

[안드로이드 디벨로퍼스 - Input Marger](https://developer.android.com/reference/androidx/work/InputMerger#public-methods_1)

[JeongUPark - Android - WorkManager (5) - Chaining Work](https://jeongupark-study-house.tistory.com/36)