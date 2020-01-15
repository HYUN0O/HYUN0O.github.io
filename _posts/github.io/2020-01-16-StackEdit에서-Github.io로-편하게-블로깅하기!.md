---
categories: 
   - Github.io
tags:
   - Minimal Mistakes
   - Github.io
---
처음 글 4~5개 정도는 github에서 일일히 썼는데, 하다보니 이거 사람이 할 짓이 안되는 것 같다.

![(그림1)](https://github.com/donggi9313/donggi9313.github.io/blob/master/assets/image/20200115/stackedit1.jpg?raw=true)

내가 쓴 글 보려면 일일히 프리뷰 눌러봐야하고, 문법 계속 검색하면서 쓰는건... 사람이 할 짓이 아니다.

> ide도 온라인 ide가 있는데, 마크다운 문법 같은 간단한 html 문법을 더 쉽게 작성할 수 있는 페이지 하나가 없을까?

하는 마음에 **마크다운 에디터**를 검색했고, 당연히 있었다!

IOS나 mac에서만 동작하는 마크다운 에디터도 많았지만, 나는 windows10-android 사용자이기 때문에 전혀 쓸모가 없었다.

좀 더 찾아본 결과, [StackEdit](https://stackedit.io/app#)이라는 마크다운 에디터가 그렇게 호환성도 좋고 클라우딩도 지원하고, 보기도 편하고 머 암튼 좋은거 많다고 그렇게 말하더라!

잡설이 길었다!

아무튼 StackEdit를 사용해서 github.io에 편하게 글을 작성하고, 자동으로 업로드, 태그까지 달아주는 기능까지 묶어서 설명해보겠다!

시작하기 전, 이 글은 **minimal-mistakes와 StackEdit**사용 환경에서 작성되었다.

# StackEdit에서 Github.io로 편하게 블로깅하기!

## ☝ 기본 사용 환경 설정

우선은 [StackEdit](https://stackedit.io/app#) 홈페이지부터 이동하자! 
바로 위의 글자를 클릭하면 이동한다.

![(그림2)](https://github.com/donggi9313/donggi9313.github.io/blob/master/assets/image/20200115/stackedit2.jpg?raw=true)

그러면 이렇게 생긴 페이지가 뜰 텐데, 빨간 상자 안에 있는 `START WRITING`을 클릭해주자!

![(그림3)](https://github.com/donggi9313/donggi9313.github.io/blob/master/assets/image/20200115/stackedit3.jpg?raw=true)

그러면 이제 웬 영어가 엄청나게 길게 쓰여있는 페이지가 나온다!
마크다운 문법에 대해 자세히 알고 싶으시다면 읽어도 되긴 하는데...

그냥 구글에 검색해서 한글로 된 페이지에서 읽자.

영어로 된 긴 사용 설명서는 제껴두고 오른쪽에 있는 **#이 들어있는 네모**를 눌러보자!

![(그림4)](https://github.com/donggi9313/donggi9313.github.io/blob/master/assets/image/20200115/stackedit4.jpg?raw=true)

짠! ```Sign In With Google```를 눌러서, 자신의 아이디로 로그인하자!

![(그림5)](https://github.com/donggi9313/donggi9313.github.io/blob/master/assets/image/20200115/stackedit5.jpg?raw=true)

로그인에 성공했다면, 본인의 닉네임과 프로필 사진이 나올 것이다.

로그인이 되어있다면 해당 계정의 workspace에 자신의 문서들이 자동으로 **백업**된다.

뿐만 아니라, 다른 환경에서 같은 아이디로 로그인만 한다면 언제, 어디서든 자신의 문서를 편집할 수 있을 것이다.

사실 이 기능이 제일 쩌는 것 같아서 StackEdit을 선택했다ㅎㅎ

자신의 옵션들도 백업이 되기 때문에, 내가 사용하는 다크 테마가 로그인하자마자 바로 불러와졌다!

![(그림6)](https://github.com/donggi9313/donggi9313.github.io/blob/master/assets/image/20200115/stackedit6.jpg?raw=true)

나같은 다크테마를 선호하는 사람도 있을테니, 다크테마로 변경하는 방법을 간단히 설명하자면 아래와 같다.

 해당 메뉴의 최하단에 존재하는 ```Settings```를 선택한 후, 아래의 코드 한 줄을 입력하고 ```OK```버튼을 누르면 끝이다!
 
```colorTheme: dark```

## 🤞 새 파일 만들기
![(그림17)](https://github.com/donggi9313/donggi9313.github.io/blob/master/assets/image/20200115/stackedit17.jpg?raw=true)

이야기가 잠깐 샜는데, 다시 돌아와서 이제 자신의 새로운 문서를 만들어보자.

윈도우 사용자라면 많이 본 폴더 아이콘이 왼쪽 위에 있을 것이다. 누르면 위의 그림과 같은 좌측 메뉴가 나오게 된다.

좌측 메뉴의 상단에 보면 여러 아이콘들이 있는데, 그 중 가장 좌측의 아이콘을 누르면 새로 만들 파일의 이름을 입력하는 것으로 파일을 생성할 수 있다!

나는 **2020-01-15-Hello,-StackEdit!**라는 이름으로 파일을 생성하겠다.
파일 이름은 반드시 규칙대로 짓지 않아도 되지만, 나는 따로 title를 지정해주기 싫기 때문에 규칙대로 지어주겠다.

확장자는 붙이지 않아도, 깃에 업로드할 때 자동으로 .md 확장자로 업로드된다.

![(그림8)](https://github.com/donggi9313/donggi9313.github.io/blob/master/assets/image/20200115/stackedit8.jpg?raw=true)

웬 이상한 영어 한 줄과, 반으로 갈라진 페이지가 하나 등장했을 것이다.

좌측은 내가 입력하는 에디터, 우측은 내가 입력한 내용을 마크다운 문법을 반영하여 보여주는 Viewer이다.

마크다운 문법에 대해서까지 서술하면 글이 너무 길어질 것 같으므로, 아까 로그인 할 때 열었던 메뉴 하단의 Markdown Cheat Sheet를 참조하거나, [이 링크](https://gist.github.com/donggi9313/7c2ba1c5e3f923e85411fb740bf514fa)에서 대충 훑어보는 것으로 대체하겠다. (내가 쓴 글은 아니다!)

새 파일을 만들고, 적당한 글 까지 모두 작성했다면 이제는 깃에 파일을 업로드해보자.

## 🤟 github에 commit하기
일단, 당연히 마크다운 문법으로 작성된 파일을 복사+붙여넣기 해서 직접 깃에서 해도 되지만, 우리는 그 짓거리가 하기 싫어서 지금 이 글을 읽고 있지 않는가?

**StackEdit는 깃에 커밋까지 버튼 하나로 해준다!**

깃으로 블로깅을 하는 사람에게는 정말로 강력한 기능 중 하나라고 생각한다.

전에 로그인했던 우측의 메뉴를 다시 열면 ```Accounts```라는 항목을 볼 수 있다.

클릭하자!

![(그림9)](https://github.com/donggi9313/donggi9313.github.io/blob/master/assets/image/20200115/stackedit9.jpg?raw=true)

이런 화면이 나올 것이다.

```Add Git Accout```를 눌러서, 내 깃 아이디로 로그인을 진행해주자.

로그인할 때 체크박스가 하나 있을텐데, 이건 내 privte 레파지토리에의 접근을 허용하겠냐는 질문이다.

본인의 private주 레파지토리에 업로드 할 생각이 없다면 체크를 해제해주면 된다.

![(그림10)](https://github.com/donggi9313/donggi9313.github.io/blob/master/assets/image/20200115/stackedit10.jpg?raw=true)

로그인 했다면, 아까 그 메뉴에 ```Publish```라는 버튼이 생겼을 것이다!
버튼을 눌러주자.

![(그림11)](https://github.com/donggi9313/donggi9313.github.io/blob/master/assets/image/20200115/stackedit11.jpg?raw=true)

우리는 github에 올리려는 것이니, ```Publish to GitHub```를 클릭해주자.

![(그림12)](https://github.com/donggi9313/donggi9313.github.io/blob/master/assets/image/20200115/stackedit12.jpg?raw=true)

이런 창이 나올텐데, 위에서부터 차례대로 내 레파지토리 URL, 파일을 업로드 할 경로, branch<sub>(비워도 된다.)</sub>, 템플릿 순서으로 입력해주면 된다.

템플릿에 대해서는 아래에서 후술할 것이니, 일단은 ```OK```를 눌러주자
.

성공적으로 업로드가 되었는데, 내 블로그에 글이 잘 올라갔는지 확인해보자.

![(그림13)](https://github.com/donggi9313/donggi9313.github.io/blob/master/assets/image/20200115/stackedit13.jpg?raw=true)

짠! 이런 식으로 글을 작성할 수 있었다.

## 🍀 템플릿으로 편하게 태그달고 분류하기
위의 방법대로 글을 성공적으로 올리는 데 까지 성공하였다!

하지만 태그를 달거나, 카테고리가 분류되지 않았다.

이러면 다시 github로 가서 ```Edit```으로 직접 넣어야하나...?라고 생각 할 수 있지만, 템플릿 기능으로 간단히 분류 할 수 있다.

우측의 메뉴를 열고 ```Templates```를 눌러보자!

![(그림14)](https://github.com/donggi9313/donggi9313.github.io/blob/master/assets/image/20200115/stackedit14.jpg?raw=true)

이런 창이 나올텐데, 기본으로 ```Jekyll site``` 로 설정되어 있을 것이다.

이걸 ```Plain Text```로 바꾸고, 두 개의 아이콘 중에서 오른쪽 아이콘을 눌러주자.

![(그림15)](https://github.com/donggi9313/donggi9313.github.io/blob/master/assets/image/20200115/stackedit15.jpg?raw=true)

이런 창이 나올텐데,  텍스트 입력 창 최상단에 본인이 원하는 카테고리나, 태그 등을 입력해주자.

카테고리마다 공통적으로 입력되는 태그들을 넣어두면 나중에 수정할때 편하고, 태그를 까먹어서 혼재되는 일을 줄일 수 있다.

![(그림16)](https://github.com/donggi9313/donggi9313.github.io/blob/master/assets/image/20200115/stackedit16.jpg?raw=true)

이런 식으로 입력하면 된다.

다시  ```Publish```버튼을 누르고, 다시 글을 등록해보자.

![(그림17)](https://github.com/donggi9313/donggi9313.github.io/blob/master/assets/image/20200115/stackedit17.jpg?raw=true)
```File publishcation```을 누르면, 기존의 publish 설정이 존재하는데, 이거는 삭제해주면 된다.

삭제했다면 다시 ```Publish to GitHub```를 클릭해주자.

![(그림18)]
(https://github.com/donggi9313/donggi9313.github.io/blob/master/assets/image/20200115/stackedit18.jpg?raw=true)
다른 입력값은 방금 전과 동일하게 해주고, 가장 아래의 Templates만 방금 만든 템플릿으로 바꿔주고, 다시 ```OK```를 눌러보자!

다시 글이 올라갔을테니, 다시 블로그에서 글을 확인해보자.

이런 식으로 중복으로 글이 올라갈 때는, 기존의 글을 덮어씌우게된다.

![(그림19)](https://github.com/donggi9313/donggi9313.github.io/blob/master/assets/image/20200115/stackedit19.jpg?raw=true)

짜잔! 카테고리와 태그가 정상적으로 등록되었다.

카테고리 뒤에 반점을 붙였더니, 다른 카테고리로 인식 해서 공백이 들어간 것 같다;

카테고리 뒤에는 반점을 붙이지 말자는 교훈을 얻었다!

## 👏 마치며
첫 세팅이 힘들지, 익숙해지면 확실히 편하다!

나중에 더 익숙해지면 태그나 카테고리 분류도 본문에서 추가로 달 수 있는 방법에 대해 포스팅하겠다.

