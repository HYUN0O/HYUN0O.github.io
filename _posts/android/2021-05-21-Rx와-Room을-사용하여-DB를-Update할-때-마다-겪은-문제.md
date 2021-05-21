---
categories: 
   - Android
tags: 
   - Kotlin
   - Android
   - Rx
   - Room
---





## 겪었던 문제



```kotlin
// class TrackListItem (trackEntity: TrackEntity, isRefreshing: Boolean, isRefreshed: Boolean)
// data class TrackEntity (@PrimaryKey val trackId: String, ...)

rxDaoSelectAll  // -> PublishSubject<Boolean>
    .observeOn(Schedulers.newThread())
    .filter { it }
    .switchMap {
        log.e()
        dao.selectAll() // SELECT * FROM Track
    }
    .observeOn(AndroidSchedulers.mainThread())
    .subscribe ({ items ->
        log.e(items) // dao.update(...) 호출 시 이 부분부터 로그가 남음.
        val trackItems = mutableListOf<TrackListItem>()
        for (item in items) {
            trackItems.add(TrackListItem(item, false, false)) 
        }
        _lvMyTracksList.value = trackItems
    }, {
        it.message?.let { msg -> log.e(msg) }
    }).addCompositeDisposable()

rxDaoUpdate  // -> PublishSubject<TrackEntity>
    .observeOn(Schedulers.newThread())
    .subscribe ({ item ->
        log.e("refreshed item.trackId -> ${item.trackId}")
        dao.update(
            TrackEntity(item.trackId, dao.selectItemNameById(item.trackId), item.fromName, item.carrierId, item.carrierName, item.recentTime, item.recentStatus)
        )	//  @Update fun update(item: TrackEntity)
    }, {
        it.message?.let { msg -> log.e(msg) }
    }).addCompositeDisposable()
```



`rxDaoUpdate`를 이용하여 DB에 데이터를 업데이트 할 때 마다, 자동으로 `dao.selectAll()`이 호출 되는 문제를 겪었다.



신기하게도, `rxDaoSelectAll`이 호출되는 것도 아닌,  `dao.selectAll()`부터 호출된다는 점이 더욱 혼란스러웠다.

`rxDaoSelectAll`의 switchMap에 있는 로그는 남지 않았으며, subscribe에 있는 로그만 계속해서 LogCat에 남았다.





## 문제 파악



위 문제가 발생하는 정확한 이유를 찾지 못했기에, 아래의 동작들을 시행하며 문제가 발생하는 **조건**을 먼저 파악했다.



1. A라는 항목을 `SELECT`하고, A가 아닌 다른 항목을 `UPDATE`

   - 그래도 A를 select한 이후의 subscribe가 진행 됨.

     > 어떤 데이터를 select했는지의 여부와는 관계가 없음.

     > 해당 DB 전체의 변화를 감지하여 다시 select를 진행하나?



2. MainFragment 에서 `SelectAll`을 진행한 후, 다른 Fragment에서 DB를  `UPDATE`

   - MainFragment로 돌아올 때, 다시 subscribe한 함수들이 진행 됨.

     > Select문이 dao 내의 테이블의 데이터를 Observe하고 있는건 아닌가?





## 해결 방안

2021년 5월 7일 현재, 정확한 정보를 찾지는 찾지는 못했지만, 위의 가설을 바탕으로 일단은 문제를 해결했다.

이후, 더 근본적인 해결 방법을 알게 되면 게시글을 수정해서 업데이트 해야겠다.

해결한 방법은 아래와 같다.



### dao.select()의 앞 뒤로 filter 사용.

```kotlin
rxDaoSelectAll
    .observeOn(AndroidSchedulers.mainThread())
    .filter {
        lvSelectAllSwitch.value = true
        it
    }
    .observeOn(Schedulers.newThread())
    .switchMap {
        log.e()
        dao.selectAll()
    }
    .filter {
        lvSelectAllSwitch.value
    }
    .observeOn(AndroidSchedulers.mainThread())
    .subscribe ({ items ->
        log.e(items)
        lvSelectAllSwitch.value = false

        val trackItems = mutableListOf<TrackListItem>()
        for (item in items) {
            trackItems.add(TrackListItem(item, false, false))
        }
        lvMyTracksList.value = trackItems
    }, {
        it.message?.let { msg -> log.e(msg) }
    }, {
        log.e()
    }).addCompositeDisposable()
```



별로 마음에 드는 해결방법은 아니지만, 더 이상 이 문제에 이 이상의 시간을 투자할 수 없다고 판단하여 강제로 막는 것으로 정리했다.

`dao.selectAll()`을 호출하기 전에, 미리 `lvSelectAllSwitch.value`를 true로 만들어두고, 호출 후에 해당 값을 체크하고, 다시 false로 되돌려주는 것으로 일단 해결했다.



나중에 더 근본적인 깔끔한 해결방안을 찾으면 다시 공유해서 업데이트 해야겠다.







-