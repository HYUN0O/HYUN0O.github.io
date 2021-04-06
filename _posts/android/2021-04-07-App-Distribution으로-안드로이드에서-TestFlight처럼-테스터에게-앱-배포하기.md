---
categories: 
   - Android
tags: 
   - Kotlin
   - Android
   - Rx
---









안드로이드 앱을 팀 규모로 개발하다보면 여러 명의 테스터들에게 테스트 앱을 설치해줘야 하는 경우가 생긴다.

1인 개발이거나 테스터의 수가 많지 않다면 직접 usb에 연결하여 빌드하는 방식으로 처리할 수 있겠지만, 사람들이 많아지거나 물리적 거리가 멀다면 이런 방법은 굉장히 불편하다.

매 번 apk 파일을 첨부한 메일을 보내는 것도 일이고, Play Console의 **공개/비공개/내부 테스트**는 구글의 심사 과정을 거쳐야 하며, 유일한 버전 코드를 지녀야 하는 등 관리하기 귀찮다.



그래서 오늘 소개하고자 하는 기능은 Firebase에서 제공하는 [App Distribution](https://firebase.google.com/products/app-distribution?hl=ko)이다.





## App Distribution의 장점

한 줄로 말하자면, Android의 내부 앱 공유와 iOS의 Test Flight의 장점을 모아 둔 것 같았다.



내가 한 달 넘는 기간 동안 App Distribution을 사용하면서 느낀 장점들은 아래와 같다.





### 별도의 검토가 필요 없음.

안드로이드의 내부 앱 공유 처럼 스토어로부터의 검토를 받지 않아도 된다.

사소한 수정 등으로 매 번 테스트 버전을 검토받아야 한다면 일의 흐름은 물론 시간적인 측면에서도 계속해서 손해를 보게 된다.



### 매 apk마다 다시 공유할 필요 없음.

![apk 관리](https://github.com/donggi9313/donggi9313.github.io/blob/master/assets/image/20210407/00.jpg?raw=true)

> Firebase > 좌측 메뉴 App Distribution > 출시 버전

iOS의 Test Flight와 같이 하나의 앱으로 모든 테스트용 앱 파일과 버전을 관리/접근 가능하다.

내부 앱 공유처럼 매 버전마다 새로운 url을 공유하지 않아도 된다.



### Play Console의 내부 앱 공유(60일)보다 훨신 더 긴 보관 기간(150일).

구버전 앱에서 발생한 오류를 추적할 경우, 예전에 공유했던 앱을 다시 찾아야 하는 경우가 생긴다.

이를 대비할 수 있는 텀이 더 길다.





### 테스터 관리에 용이함.

![테스터 관리](https://github.com/donggi9313/donggi9313.github.io/blob/master/assets/image/20210407/01.jpg?raw=true)

> Firebase > 좌측 메뉴 App Distribution > 테스터 및 그룹

'테스터 및 그룹' 탭에서 위 사진과 같이 테스터를 관리 할 수 있다.

특정 그룹에 있는 테스터에게만 앱을 배포할 수도 있고, 





### iOS와 Android 앱을 동시에 관리 가능.

내 작업 환경에서는 Android만 App Distribution으로 공유를 진행하여 직접 경험해 본 적은 없지만, App Distribution에서 주장하는 강력한 장점이라고 한다.





## 사용 방법 (개발자)

App Distribution을 사용하기 위해서는, 우선 내 앱을 Firebase에서 관리하고 있어야 한다.

해당 방법은 구글에 **Android Firebase 연동** 을 검색하면 많은 글이 있으니 참고하면 된다.

연동이 이미 되어있다는 가정 하에 사용 방법은 아래와 같다.





### 테스터 등록 및 그룹 추가

테스터를 등록하는 방법은 2가지가 있다.





#### 초대 링크를 활용한 방법

![테스터 관리](https://github.com/donggi9313/donggi9313.github.io/blob/master/assets/image/20210407/02.jpg?raw=true)

> Firebase > 좌측 메뉴 App Distribution > 초대 링크

위 사진 처럼 특정 그룹으로 초대하는 링크를 생성한 후, 해당 링크를 공유하는 것으로 테스터를 초대할 수 있다.





#### 직접 이메일 주소를 적어 초대하는 방법

![테스터 관리](https://github.com/donggi9313/donggi9313.github.io/blob/master/assets/image/20210407/03.jpg?raw=true)

> Firebase > 좌측 메뉴 App Distribution > 테스터 및 그룹

위 사진처럼 이메일 주소를 직접 적어서 테스터에게 메일을 보내 테스터로 초대할 수 있다.





### apk 업로드 및 테스터 지정

![apk 업로드](https://github.com/donggi9313/donggi9313.github.io/blob/master/assets/image/20210407/04.jpg?raw=true)

> Firebase > 좌측 메뉴 App Distribution > 출시 버전

위 영역에 내 apk 파일을 드래그 하면 쉽게 업로드 가능하다.

다만, 기존에 이미 업로드 한 파일은 검증 단계에서 자동으로 걸러진다.



### 

![테스터 지정](https://github.com/donggi9313/donggi9313.github.io/blob/master/assets/image/20210407/05.jpg?raw=true)

대상 테스터나 그룹을 지정할 수 있다.

특정 버전의 경우, 원하는 대상에게만 앱을 배포하는 것도 가능하다.







## 사용 방법 (테스터)



아래 과정은 테스트 앱을 설치할 휴대기기로 진행하는 것을 권장한다.

데스크톱에서는 2021년 4월 6일 현재까지는 테스터 초대 수락이 불가능하기 때문이다.



### 본인이 테스터로 접근 할 이메일 작성(초대 링크로 초대 받은 경우) 

![이메일 작성](https://github.com/donggi9313/donggi9313.github.io/blob/master/assets/image/20210407/06.jpg?raw=true){: width="50%" height="50%"}

본인이 초대 링크를 통해 초대 받은 경우가 아니라면 이 다음 단계부터 진행하면 된다.

빈 칸에 본인이 사용할 이메일을 작성하고, '가입'버튼을 누르면 된다.





### 받은 편지함 확인

받은 편지함에 도착한 메일에서 'Get Started'를 클릭한다.



### 초대 수락

![초대 수락](https://github.com/donggi9313/donggi9313.github.io/blob/master/assets/image/20210407/07.jpg?raw=true){: width="50%" height="50%"}

이동한 페이지에서 '초대 수락' 버튼을 눌러주자

만약, **데스크톱에서 접근 시**에는 '초대 수락'버튼이 **보이지 않는다**.



### 다운로드 및 테스트 진행

![다운로드 및 테스트 진행](https://github.com/donggi9313/donggi9313.github.io/blob/master/assets/image/20210407/08.jpg?raw=true){: width="50%" height="50%"}

원하는 버전의 앱을 다운로드하고, 테스트를 진행하면 된다.

만약 본인이 자주 사용하는 기기라면, 하단의 **'App Tester 다운로드'** 를 통해 앱을 설치한다면 일련의 과정을 반복할 필요 없이 더 쉽게 테스트 앱을 관리 할 수 있도록 도와준다.



