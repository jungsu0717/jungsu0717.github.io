---
title: "MVC,MVP,MVVM 패턴 비교"
date: 2020-06-30 11:48:28 -0400
categories: etc
---

디자인 패턴 정리 및 비교
구글 아키텍처 문서를 보면 ViewModel 을 이용한 MVVM 패턴을 권장
https://developer.android.com/jetpack/docs/guide


# 1. MVC	
Model + View + Controller 의 약자

장점 : 널리 사용되고 있는 패턴이라는 점에 걸맞게 가장 단순하고 적용이 쉬움
단점 : MVC 패턴의 단점은 View와 Model 사이의 의존성이 높아 복잡해지고 유지보수가 어려워 질 수 있음

![mvc](https://user-images.githubusercontent.com/6903839/86111626-f51e8180-bb01-11ea-9975-f75425ec5dbb.png)

Model : 어플리케이션에서 사용되는 데이터와 그 데이터를 처리
View : 사용자에서 보여지는 UI
Controller : 사용자의 입력(Action)을 받고 처리

# 2.MVP	
Model + View + Presenter 의 약자

장점 : Presenter 를 통해서만 데이터를 전달 하기때문에 View와 Model의 의존성이 없다.
단점 : View와 Model의 의존성은 해결했지만, View와 Presenter 의 의존성이 높아져 기존 MVC 패턴의 단점을 완벽히 해결하며 새로운 문제점 발생



Model : 어플리케이션에서 사용되는 데이터와 그 데이터를 처리
View : 사용자에서 보여지는 UI
Presenter : View에서 요청한 정보로 Model을 가공하여 View에 전달, View와 Model의 매개채 역할

# 3.MVVM
(시사상식 적용)	
Model + View + ViewModel 의 약자

장점 : Command 패턴과 Data Binding 을 이용해서 View 와 View Model의 의존성을 없애고 View와 Model의 의존성또한 없어서 Dagger를 통해 모듈화 하여 개발 가능
단점 : View Model의 설계가 어렵고 공부하다 멘붕이 올수도...


Model : 어플리케이션에서 사용되는 데이터와 그 데이터를 처리
View : 사용자에서 보여지는 UI
View Model : View를 나타내 주기 위한 Model이자 View를 나타내기 위한 데이터 처리
