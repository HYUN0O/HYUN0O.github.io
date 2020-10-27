---
categories: 
   - study
tags:
   - Kotlin
   - Rx
---



Rx를 이용하여 데이터를 관리하다보면, 흐름제어는 필수적이다.

사용자가 빠르게 클릭하여 발생하는 데이터에 대한 제어, 너무 많은 데이터가 몰려오는 경우에 대한 제어 외에도 아주 많은 경우들이 존재한다.

기존에는 락을 걸고 입력을 방지하거나, 타이머를 설정하여 일정 시간동안 입력을 무시하는 등의 방법을 이용했으나, 예상치 못한 경우나 사소한 실수로 코드의 흐름이 꼬여, 기능의 사용이 어려워 질 수 있다.

이런 경우, Rx를 이용하여 더 효율적인 흐름 제어가 가능한 방법에 대해 알아보려고 한다.

## Flow Control

데이터는 항상 내가 원하는 순서로, 형태로, 예쁘게 전달되지 않는다.

사용자의 조작, 네트워크 문제, 혹은 원래 그렇거나, 한 순간에 몰려올 수도 있고, 경우는 다양하다.

Rx의 흐름제어를 이용하면 까다로운 흐름 제어도 간단하게 처리할 수 있다.

오늘은 코드를 짜는 것 보다는 나중에 "아 이런게 있었지!"하고 검색해서 찾아서 쓸 수 있도록 개념만 정리하려고 한다. 구글링도 개발자의 실력이니까.



### 1. ThrottleFirst()

![throttleFirst](https://github.com/danggai/danggai.github.io/blob/master/assets/image/20201027/throttleFirst.png?raw=true)

ThrottleFirst는 windowDuration(시간 간격), Unit(시간 단위)를 인자로 받는다.

ThrottleFirst는, 어떤 데이터 입력 후, 일정 시간동안의 추가 입력을 **무시**한다.

예를 들어 어떤 버튼을 눌렀을 때 애니메이션을 충분한 시간만큼 출력해야 한다면 좋은 기능일 것이다.



### 2. ThrottleLast(), Sample()

![throttleLast](https://github.com/danggai/danggai.github.io/blob/master/assets/image/20201027/throttleLast.png?raw=true)

![sample](https://github.com/danggai/danggai.github.io/blob/master/assets/image/20201027/sample.png?raw=true)

ThrottleLast는 intervalDuration(시간 간격), unit(시간 단위)를 인자로 받는다.

Sample는 period(시간 간격), unit(시간 단위)를 인자로 받으며, 추가로 emitLast라는 인자를 받을 수 있다.

ThrottleLast과 Sample는, 둘 다 일정 시간동안 입력된 데이터 중 **마지막** 데이터를 발행한다.

ThrottleLast는 얼핏 보면, ThrottleFirst와 반대되는 어휘가 사용되었기에 정 반대의 기능을 할 것 같지만, 실제로는 그렇지 않다.

오히려 ThrottleFirst는 후술 할 Debounce와 반대에 더 가깝다.



Sample는 얼핏 봐도 ThrottleLast와 비슷하게 생겼고, 실제로 수행하는 기능도, 기본 개념도 비슷하다.

두 함수의 원형을 살펴보면, Sample는 추가로 emitLast라는 인자를 넣을 수 있도록 오버로딩 되어 있다.

해당 인자의 값은 기본적으로 false로, true로 바꿀 경우 Timeout를 기다리는 동안 observable이 종료되는 경우, 남아있는 값 중 마지막 값을 발행하게 된다.



### 3. Debounce()

![debounce](https://github.com/danggai/danggai.github.io/blob/master/assets/image/20201027/debounce.png?raw=true)

Debounce는 timeout(시간 간격), unit(시간 단위)를 인자로 받는다.

Debounce는 어떤 데이터가 입력된 후 timeout(회색 부분) 동안 추가 이벤트를 입력받지 않으면, 입력받은 마지막 데이터를 발행한다.

말이 복잡하다면, 그림으로 보면 더 빨리 이해될 것이다. 

검색 자동완성 기능을 구현할 때, 문자열을 observable 시켜둔 경우 적절한 대기시간을 준다거나, 연타가 가능한 버튼을 구현할 때 기능 최적화에 유용할 것으로 보인다.



### 4. Window()

![window3](https://github.com/danggai/danggai.github.io/blob/master/assets/image/20201027/window3.png?raw=true)

Window는 count(카운팅 할 값의 갯수)를 인자로 받는다.

count 개 만큼 값을 받으면, 모인 데이터를 단일 obserable로 만들어 구독자에게 발행한다. 

시간에 따라 구독자에게 값을 발행하는 위의 함수들과는 달리 count를 인자로 갖기 때문에 비동기 작업이라고 할 수 없다.



### 5. Buffer()

![buffer3](https://github.com/danggai/danggai.github.io/blob/master/assets/image/20201027/buffer3.png?raw=true)

Buffer는 count(카운팅 할 값의 갯수)를 인자로 받으며, skip이라는 인자를 추가로 받을 수 있다.

skip 는 반드시 count를 초과하는 수여야 하며, 만약 count=2, skip=3일 경우, 2개의 값을 수집하고, 1개의 값은 스킵한다.



## 참고 사이트

  - [reactivex.io](http://reactivex.io/RxJava/javadoc/io/reactivex/Flowable.html#flatMap-io.reactivex.functions.Function-)