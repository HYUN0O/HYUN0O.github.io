---
categories: 
   - TIL
tags:
   - Android
---
TIL은 Today I Learned의 약자로, 오늘 내가 배운 내용에 대한 간단한 정리를 하는 포스트입니다.

# Android View, View Group, Layout

오늘, 나는 안드로이드 운영체제의 View, View Group, Layout에 대하여 공부하였다.

이 세 가지 구성요소는 안드로이드 UI를 구성하는데 있어 가장 기본적인 개념이다.

이 것이 중요한 이유는, 안드로이드 운영체제는 ios에 비해 수 많은 종류의 기기들의 호환성을 만족하여야 하기 때문에 더욱 신중하게, 호환되도록 구성하여야 한다.

## ☝ View

모든 안드로이드 UI의 구성요소는 모두 **View** 라는 클래스를 상속받는다. (View는 물론 Object로 부터 상속받았다.)

이는 안드로이드 어플리케이션에서 화면에 표현되어는 모든 것을 의미한다.

View가 모여 Activity를 구성하며, Activity는 Task로의 할당으로 하여금 하나의 App으로서 동작한다.

이 View는 **계층 구조**<sub>Hierarchy Architecture</sub> 로 이루어져 있으며, 상속의 상속의 상속....을통해 자주 사용되는 여러가지 클래스들로 구현되어있다.

버튼, 이미지, 리스트, 등등 모두가 View를 상속받아 만들어진 클래스의 일종이다.

## 🤞 View Group

View Group는 여러가지 View를 담는 그릇<sub>Container</sub>의 역할을 한다. 

기본적으로 직접적으로 눈에 보이는 형태는 아니며, backgroundcolor 등의 속성 값<sub>attributes</sub>을 이용해 눈에 보이도록 설정할 수 있기는 하다.

이런 View Group를 각자의 특성을 가지도록 구현해둔 클래스를 우리는 Layout 이라고 한다.

## 🤟 Layout

Layout들은 기본적으로 View Group의 서브 클래스들로, View나 View Group을 화면에 배치할 때, 이를 제어하기 위해 설계된 클래스이다.

일반적으로 Layout의 클래스 명은 (특징)Layout 의 형태를 하고 있다.

몇 년 전까지는 **RelativeLayout**<sub>상대 레이아웃</sub>이 가장 널리 사용되었으나, 글을 작성하는 현재는 **ConstraintLayout**<sub>제약 레이아웃</sub>이 그 자리를 차지하였다.

이 외의 Layout로도 **ConstraintLayout**이 하는 기능을 충분히 수행할 수 있었으나, 점점 안드로이드 기기가 다형화되고, 다양한 해상도를 지원해야함에 있어 더욱 많은 기능을 지원하는 **ConstraintLayout**이 널리 사용되게 되었다.

ConstraintLayout은 Relative Positioning<sub>상대 배치</sub>은 물론, Circular Positioning<sub>원형 배치</sub>, Visibility Behavior<sub>가시성에 따른 동작</sub>, Dimension Constraints <sub>크기 및 치수에 대한 제약조건</sub> 등의 다양한 개발자 편의 기능들을 제공하는 꿀 레이아웃이다.


> 참고한 블로그 : [https://www.charlezz.com/?p=669](https://www.charlezz.com/?p=669)

