---
categories: 
   - Study
tags:
   - Postman
---

오늘은 Postman을 이용해 API를 테스트하고, 커스텀 헤더를 설정하는 방법과, 테스트 전/후로 함수 등을 실행하여 데이터를 처리하는 방법을 알아보려고 한다.

# Postman
## ☝ Postman 설치 및 기본 테스트

Postman은 [이 링크](https://www.postman.com/downloads/)에서 다운받을 수 있다. 자신의 윈도우 비트 버전에 맞춰 다운로드 받으면 된다.

다운로드 후 실행하면 아래 화면을 볼 수 있다.

![그림1](https://github.com/danggai/danggai.github.io/blob/master/assets/image/20200220/01.png?raw=true)

Postman은 위처럼 **Collection**이라는 자동화 기능을 지원한다.

해당 기능을 이용해 우선은 기본적인 API 테스트부터 진행해보자.

![그림2](https://github.com/danggai/danggai.github.io/blob/master/assets/image/20200220/02.png?raw=true)

이름은 적당히 "TEST COLLECTION"으로 설정하였다.

Collection은 여러가지의 Request의 모음으로 만들어 질 수 있다.

아래 그림과 같이 Request를 생성해주자.

![그림3](https://github.com/danggai/danggai.github.io/blob/master/assets/image/20200220/03.png?raw=true)
	
![그림4](https://github.com/danggai/danggai.github.io/blob/master/assets/image/20200220/04.png?raw=true)

1. Request의 이름.
2. Request에 대한 설명. 마크다운 문법을 지원한다.

![그림5](https://github.com/danggai/danggai.github.io/blob/master/assets/image/20200220/05.png?raw=true)

API를 호출할 메소드 형식, 주소, 헤더, 바디, 쿼리 등을 입력하고 SEND 버튼을 누르면 API 호출 결과를 받을 수 있다.

나는 네이버 언어 감지 API를 통해 테스트하였다.

이 경우, `body`의 `query`가 hello, 즉 언어 감지를 원하는 대상이 영어이므로 API가 `'en'`을 반환하였다.

![그림6](https://github.com/danggai/danggai.github.io/blob/master/assets/image/20200220/06.png?raw=true)

만약 https로 호출 할 때 `Error: self signed certificate in certificate chain` 오류가 발생한다면,  설정에서 `Self certificate verification` 설정을 OFF하면 된다.

## 🤞 커스텀 헤더 사용

네이버 API는 모두 네이버에서 발급하는 클라이언트 ID와 시크릿 코드를 갖는 일종의 커스텀 헤더를 필요로 한다.

![그림7](https://github.com/danggai/danggai.github.io/blob/master/assets/image/20200220/07.png?raw=true)

이 커스텀 헤더를 설정하는 방법은 복잡하지 않다.

위와 같이 `Headers`탭에서 자신의 API가 필요로 하는 키 값들을 지정하고, 담아두면 간단히 커스텀 헤더를 통해 API를 호출할 수 있다.

이 경우, 네이버 언어 감지 API가 필요로 하는 헤더 값은 [이 곳](https://developers.naver.com/docs/papago/papago-detectlangs-api-reference.md#%EC%9A%94%EC%B2%AD-%EC%98%88)에서 확인할 수 있다.

위의 Value에 담는 값은 중괄호 2쌍을 이용하여 변수를 호출하는 것도 가능하다.

## 🤟 환경 변수 설정

Postman은 환경 변수를 선언하고, 불러오는 기능도 지원한다.

![그림8](https://github.com/danggai/danggai.github.io/blob/master/assets/image/20200220/08.png?raw=true)
![그림9](https://github.com/danggai/danggai.github.io/blob/master/assets/image/20200220/09.png?raw=true)

우측 상단의 톱니바퀴를 누르고, `Manage Environments`를 누르고, `Add`를 눌러보자.

![그림10](https://github.com/danggai/danggai.github.io/blob/master/assets/image/20200220/10.png?raw=true)

1은 해당 환경 변수들의 집합의 이름이다. 2에는 사용할 환경 변수들을 넣어두면 되고, 다 입력하면 `Add`를 눌러서 정리하자.

![그림11](https://github.com/danggai/danggai.github.io/blob/master/assets/image/20200220/11.png?raw=true)

생성한 변수의 집합은 위 그림의 1번에서 지정할 수 있고, 2번에서 호출하는 것으로 API 호출에 이용할 수 있다.

## 🍀 테스트 전, 함수 실행하기

Postman은 `Pre-request Script`라는 기능을 지원한다. 이 기능은, API 호출 전에 스크립트를 수행하여 데이터를 가공하거나, 원하는 환경 하에서 API를 테스트 할 수 있게 도와준다.

![그림12](https://github.com/danggai/danggai.github.io/blob/master/assets/image/20200220/12.png?raw=true)

위 그림과 같이 `Pre-request Script`에 JavaScript로 된 스크립트를 작성하는 것으로, API 호출 전에 원하는 코드를 실행 할 수 있다.

```javascript
const number = Math.random() * 3;

if (number < 1) {
    var str = "hello?"
} else if (number < 2) {
    var str = "안녕하세요"
} else {
    var str = "bonjour"
}

pm.globals.set("my_string", str);
```

위 코드는 API 호출에 앞서, pre-request script를 통해 데이터를 정제하여 전송할 수 있다는 점을 보여주기 위해 랜덤한 변수에 따라 다른 언어의 인삿말을 전송하도록 작성한 코드이다.

또한, API 호출 시 body에서 pre-request에서 선언한 전역 변수를 호출하도록 하였다. 결과는 아래와 같다.

![그림13](https://github.com/danggai/danggai.github.io/blob/master/assets/image/20200220/13.png?raw=true)

위 결과는 연속으로 3번 호출한 API의 호출 로그이다.

항상 저 순서로 호출되는 것이 아닌, 매 호출마다 세 가지 언어 중 한 가지의 인삿말로 API를 호출하게된다.

이 방법을 이용하여, pre-request script를 통해 자신의 데이터를 가공하거나, 암호화 하는 것이 가능하다.

## 👏 테스트 후, 반환 값 테스트하기

Postman은 `Tests`라는 기능을 지원한다.  이 기능은 API 호출로 반환받은 response가 원하는 값을 포함하고 있는지, 의도한 결과인지 테스트를 통해 성공/실패로 확인할 수 있는 기능이다.

![그림14](https://github.com/danggai/danggai.github.io/blob/master/assets/image/20200220/14.png?raw=true)

위 그림과 같이 `Tests`에 JavaScript로 된 스크립트를 작성하는 것으로, API 호출 후에 원하는 코드를 실행 하고, 원하는 결과인지 확인할 수 있다.

![그림15](https://github.com/danggai/danggai.github.io/blob/master/assets/image/20200220/15.png?raw=true)

내가 작성한 테스트 코드는, 랜덤한 변수에 의해 전달되는 인삿말이 한국어일 때만 test가 PASS되고, 외의 경우는 무조건 FAIL하는 코드다.

이 방법을 활용하면 암호화된 데이터를 복호화시키거나, 원하는 결과 값이 반환되었는지 비교하는 일련의 시행의 자동화를 쉽게 할 수 있다.



