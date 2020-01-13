---
categories: 
   - Study
tags:
   - Design Pattern
---
오늘은 개발자로서 기본으로 알아야 하는 지식 중 하나인 디자인패턴중 현재 가장 유명하고 많이 쓰이고, 묶어서 배워두면 좋은 패턴인 MVC, MVP, MVVM에 대해 알아보려고 한다.

# Design Patterns
## ☝ 그게 뭔데? 왜 세 개나 있는데?

우선, 디자인 패턴은 소프트웨어 설계 시 발생하는 많은 문제들이 발생할 때, 이런 문제들을 쉽고 효율적으로 해결할 수 있도록 구성한 코딩방법, 또는 구조를 의미한다.

말이 길지, 그냥 **How to Basic**같은 느낌이다.

근데 왜 세 가지나 있냐면,  한 명의 개발자만 한 프로그램을 담당하는 일은 자주 없기 때문에, 각자가 각자의 영역을 나누어 관리하기 위해서이다. ~~할 수도 있긴 하지만~~

역할분담이 잘 이루어진다면 개발효율은 상승할 것이며, 유지보수가 편해짐 역시 당연하다.

## 🤞 그럼 그 세 가지가 뭔데?

 1. MVC <sub> Model + View + Controller</sub>
 2. MVP<sub> Model + View + Presenter</sub>
 3. MVVM<sub> Model + View + View Model</sub>

이렇게 총 세 가지 디자인 모델이 존재한다.

각 모델을 구성하는 요소와 역할은 다음과 같다.

 1. Model
	 - 어플리케이션에서 사용되는 데이터와, 그 데이터를 처리함.
 2. View
	- 사용자에게 보여지는 UI
 3. Controller
	- 사용자의 입력을 받고, 처리함.
 4. Presenter
	 - Model을 View가 요청하는 형식으로 가공하여 전달함.
 5. View Model
	 - View를 위한 Model.

이 정도면 개념적인 설명은 충분히 한 것 같다.
다음은 세 모델의 비교를 해보자!


## 🤟 그래서 걔네가 뭐가 다른데?

### 1. 동작 방식
 1. MVC <sub> Model + View + Controller</sub>
	 1. 사용자의 동작을 Controller이 입력받음.
	 2. Controller이 입력받은 동작을 Model으로 전달함.
	 3. Model이 전달에 대해 처리하여 View으로 전달함.
	 4. View는 Model로 받은 새 데이터로 변경 된 UI를 표시함.

 2. MVP<sub> Model + View + Presenter</sub>
	 1. 사용자의 동작을 View이 입력받음.
	 2. View는 Presenter에게 동작을 전달하고, 데이터를 요청함.
	 3. Presenter은 Model에게 데이터를 요청함.
	 4. Model은 Presenter로 부터 요청받은 데이터를 전달함.
	 5. Presenter은 View에게 요청받은 데이터를 전달함.
	 6. View는 Presenter로 부터 받은 새 데이터로 UI를 표시함.

 3. MVVM<sub> Model + View + View Model</sub>
 
	 1. 사용자의 동작을 View이 입력받음.
	 2. View는 Comman 패턴을 통해 View Model에 동작을 전달함.
	 3. View Model은 Model에게 데이터를 요청함.
	 4. Model은 View Model에게 데이터를 전달함.
	 5. View Model은 받은 데이터를 가공하여 저장함.
	 6. View는 View Model과 Data Binding을 통해 UI를 표시함.

### 2. 특징

 1. MVC <sub> Model + View + Controller</sub>
	 - Controller이 여러 개의 View를 조작 가능.
	 - Model은 View와 Model의 사이에서 중개하는 역할.

 2. MVP<sub> Model + View + Presenter</sub>
	 - Presenter은 1개의 View와 Model에 관계함.
	 - Presenter은 View와 Model의 사이에서 중개하는 역할.

 4. MVVM<sub> Model + View + View Model</sub>
	 - Command 패턴과 Data Binding을 사용하여 구현 됨.
		 - 이로 인해 View-View Model간의 **의존성이 없음**!
	 - 여러개의 View가 1개의 View Model에 관계될 수 있음.

### 3. 장점

 1. MVC <sub> Model + View + Controller</sub>
	 - 단순함.
	 - 단순하니 많은 사람들이 사용함
	 - 모델이 어디에 종속되지 않음.

 2. MVP<sub> Model + View + Presenter</sub>
	 -  View와 Model간의 의존성을 Presenter을 통해 해결함.

 3. MVVM<sub> Model + View + View Model</sub>
	- Model, View, View Model 사이의 의존성 문제를 해결함.
	- 각각 독립적임.
		- 유닛 테스트에 있어 매우 유리함.
	- 중복되는 코드를 모듈화 할 수 있음.

### 4. 단점

 1. MVC <sub> Model + View + Controller</sub>
	 - View와 Model 사이의 의존성이 너무 높다.
	 - 의존성은 어플리케이션을 복잡하게 만든다.
	 - Controller이 View에 강하게 종속되므로, 유닛 테스트가 용이하지 않음.

 2. MVP<sub> Model + View + Presenter</sub>
	 - Model과 View의 의존성을 해결한 대신 View와 Presenter 사이의 의존성이 높아져버림;;
	 - 필요한 클래스 수가 많아져, 어플리케이션이 복잡해짐.

 3. MVVM<sub> Model + View + View Model</sub>
	- View Model의 설계가 어려움.


## 🍀 결론

MVC를 발전시킨 것이 MVP이고, 발전하여 MVVM이 된 것이 분명 맞지만, 그렇다고 MVVM이 항상 MVC보다 좋다는 것은 아니다!

자신이 챙겨야 하는 장점과 포기할 수 있는 단점을 잘 선택하여 자신에게 맞는 모델을 선택할 수 있다면 된다.

예를 들자면, 룰렛을 돌리는 정도의 간단한 어플리케이션이라면 복잡해질 뷰 자체가 없으니  MVC만으로 개발하여도 전혀 문제가 없을 것이다! 

굳이 MVVM을 선택하여 열심히 View Model을 설계할 필요 없이, 직접적으로 Model과 View를 묶어버리면 된다.

하지만 수 십 가지의 기능을 가진 어플리케이션에서 MVC 모델을 선택하고 개발을 시작한다면.. 지옥이 펼쳐질 것이다.

위의 세 가지 모델이 세상 모든 디자인 모델의 전부가 아니니, 다음 시간에는 다른 여러가지 모델들도 같이 살펴봐야겠다! 


