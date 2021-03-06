---
toc: true
toc_sticky: true
categories:
  - Android
title: Android - RoomDB를 이용한 MVVM 아키텍처 디자인 패턴 구축 (Kotlin) (1)
tags: [Kotlin, 코틀린, JAVA, Android, AAC, LiveData, ViewModel]
excerpt: "RoomDB를 이용한 MVVM 아키텍처 디자인 패턴 구축"
---

* **연구주제** : Android - RoomDB를 이용한 MVVM 아키텍처 디자인 패턴 구축 (Kotlin)
* **연구목적** : 안드로이드에서의 코틀린 활용
* **연구일시** : 2020년 03월 05일 09:00~17:00
* **연구자** : 이재환 <ljh951103@naver.com>
* **연구장비** : HP EliteDesk 800 G4 TWR, Kotlin, Android studio, IntelliJ
* **관련연구** : Java, Android, Kotlin, AAC, LiveData, ViewModel

<br>
   
## 서론

### **MVVM 패턴**

안드로이드 프로그래밍을 하면서 액티비티에 이것저것 덕지덕지 붙이다보면 액티비티가 무거워지거나 혹은 종속성이 너무 강해 테스트가 힘들고 유지보수가 힘들어진다.  
이런 고민 떄문에 MVVM 패턴이 등장했다.  
`MVVM 패턴`은 `View` - `ViewModel` - `Model`을 이용해 각각 역할을 분리하여 가독성과 재사용성을 높인 디자인 패턴이다.
   
## 본론

### **MVC VS MVVM**

사실 익숙하게 아는 디자인 패턴중에 MVC 모델이 있다.  
MVC모델은 Model - View - Controller로 구성된다.  
액티비티가 컨트롤러의 역할을 했으며, 뷰와 연결되어 유저와 상호작용도 하고, 모델과 연결되어 데이터도 처리했다. 즉 뷰와 모델 사이에서 중재자 역할을 했다.  

MVVM에서는 뷰에서 뷰모델로, 뷰모델에서 모델로 작업을 처리하며, 뷰에서 모델을 직접 참조하지 않는다. 대신 뷰에서 뷰모델을 관찰하며 데이터의 변경 사항을 감지한다. 다음과 같이 말이다.

![image](https://user-images.githubusercontent.com/57826388/75573710-be2b5700-5aa0-11ea-8a9e-bb1aa750cd4c.png)

MVC에서는 컨트롤러가 유저의 클릭 액션을 확인하고, 모델에 데이터를 갱신하도록 요청하고, 뷰에도 화면을 업데이트 하라고 요청을 해야한다. 코딩하다 무언가 빠뜨리면, 때로는 DB만 갱신되고 화면은 갱신되지 않는 경우도 나타난다. 액티비티가 해야 할 역할이 많아 바쁘다.

MVVM에서도 뷰가 유저의 클릭 액션을 확인하지만, 뷰에서 곧바로 DB에 접근하지 않는다. 말 그대로 ‘뷰’이기 때문에 UI를 갱신하는 역할에 충실하다. 대신 뷰모델을 참조하고, 뷰모델에서는 다시 모델에서 잘 정리된 데이터를 참조한다. 또, 뷰는 뷰모델을 관찰(Observe) 한다. DB에 새로운 아이템을 추가한 후에 화면을 업데이트 하라고 직접 명령하지 않아도 된다. 뷰에서는 이미 뷰모델을 관찰하고 있기 때문에 데이터의 변화를 알아차리고 자동으로 화면을 갱신한다.

<br>

### **MVVM 장점**

1. 뷰가 LiveData 데이터를 실시간으로 관찰, 즉 Observable 패턴을 이용하기 때문에 데이터베이스를 관찰하고 자동으로 UI를 갱신한다. 

2. 생명주기로부터 안전하고 메모리 릭을 방지한다. 
   - 뷰모델을 통해 데이터를 참조하기 때문에 액티비티/프래그먼트의 생명주기를 따르지 않는다. 
   - 화면전환과 같이 액티비티가 파괴된 후 재구성 되어도 뷰모델이 데이터를 홀드하고 있기 때문에 영향을 받지 않는다. 
   - 뷰가 활성화되어있을 경우에만 작동하기 때문에 불필요한 메모리 사용을 줄일 수 있다.

3. UI, 비즈니스 로직, 데이터베이스가 기능별로 모듈화 되어있어서 역할별로 정리가 잘되어있다. 유닛 테스트가 한결 용이해질 것이다.

<br>

### **MVVM 구성/기능**

![image](https://user-images.githubusercontent.com/57826388/75574539-d3ed4c00-5aa1-11ea-9d6d-6db9a32dc716.png)

MVVM의 구성 요소로는 위의 그림에서 초록색 부분은 UI를 담당하는 뷰, 파란 부분은 뷰모델, 그리고 리포지토리와 룸 데이터베이스는 모델 역할을 담당한다.

<br>

#### **View**

UI를 담당하는 액티비티나 프래그먼트이다.  
화면에 무엇을 그릴지 결정하고, 사용자와 상호작용한다.  
데이터의 변화를 감지하기 위한 옵저버를 가지고 있다.

- Activity, Fragment, CustomView, Dialog, Toast, Snackbar, Menu 등의 UI 컴포넌트
- XML
  
<br>

#### **ViewModel**

뷰모델은 UI를 위한 데이터를 가지고 있으며, 구성이 변경되어도 살아남는다.  
AsyncTask는 액티비티나 프래그먼트의 생명 주기에서 자유로울 수 없지만, 뷰모델은 뷰와 분리되어 있기 때문에 액티비티가 Destroy 되었다가 다시 Create 되어도 종료되지 않고 데이터를 여전히 가지고 있다.

- View에 데이터가 변경되었음을 알려줄 수 있는 코드
- View에 영향을 끼칠 수 있는 Model 의 상태 관리를 위한 것
- ex. 로딩중, 네트워크 상태, 오프라인 상태 등
- View 또는 액티비티 Context 에 대한 레퍼런스를 가져서는 안된다.
  
***AAC 의 ViewModel 와 MVVM 의 ViewModel 은 다르다***

> AAC ViewModel
> 
- UI 와 관련한 데이터를 라이프 사이클을 고려한 방식으로 저장하고 관리하도록 설계된 라이브러리
- 뷰 또는 뷰의 특정 인스턴스 생성보다 오래 지속되도록 설계되어있다.
- 화면회전 등, View 의 구성이 변경되더라도 데이터가 남아 있을 수 있다.

<br>

즉 MVVM 의 ViewMode 을 만들 때, 사용하면 좀 더 편리하게 View 의 데이터를 관리할 수 있는 라이브러리이지 MVVM을 사용하여 개발하고자 할 때 무조건적으로 써야하는 것은 아니라는 것이다. 

데이터 유지가 필요한 경우가 아니라면 굳이 AAC의 ViewModel을 쓰지 않고 일반적인 ViewModel 형태로 코드를 작성해도 무방하다.

<br>

#### **Model**

- Data와 관련한 것들
- Network, DB, SharedPreference 등의 다양한 데이터소스

<br>

#### **LiveData**

라이브데이터는 관찰이 가능한 데이터 홀더 클래스이다. 뷰에서 뷰모델의 라이브데이터를 관찰하게 되면 데이터가 변경될 때 내부적으로 자동으로 알려주게 된다.  
또한 라이브데이터는 액티비티나 프래그먼트의 생명 주기를 인지한다. 즉, 액티비티가 화면 위에 활성화되어 있을 때에만 UI변경 등의 기능을 동작하게 되고, Destroy 된 상태에서는 동작하기 않기 때문에 메모리 릭의 발생을 줄여준다.

<br>

#### **Repository**

뷰모델과 상호작용하기 위해 잘 정리된 데이터 API를 들고 있는 클래스이다. 앱에 필요한 데이터, 즉 내장 데이터베이스나 외부 웹 서버 등에서 데이터를 가져온다.  
따라서 뷰모델은 DB나 서버에 직접 접근하지 않고, 리포지토리에 접근하는 것으로 앱의 데이터를 관리한다.

<br>

#### **Room**

SQLite 데이터베이스를 편하게 사용하게 해주는 라이브러리이다.  
SQLite의 코드를 직접 작성하는 경우, 직접 테이블을 Create 하거나 쿼리문을 일일이 변수를 통해 작성해주어야 했지만, Room을 쓰면 조금 더 직관적이고 편리하게 DB를 사용할 수 있다.
   
<br>

## 결론

- View, Model, ViewModel 은 서로의 존재를 알지 못해야 한다.

- ViewModel 에서 View 에게 갱신을 알리기 위해서는, Observable 또는 DataBinding 을 사용해야 한다. 즉 ViewModel 의 데이터가 수정되더라도, View 에서는 수정할 것이 없도록 둘을 분리해야 한다.

- Model 에는 데이터와 관련한 소스만, View 는 화면과 관련한 소스만 넣는다. View 에서 만약 데이터에 의한 변경 등이 있으면 ViewModel의 옵저버 등을 통해서 값이 변경되었음을 알고 변경할 수 있다.

다음 포스팅에서는 학습한 이론을 토대로 직접 코드로 구현해보겠다.
  
<br>

## 향후과제

LiveData, ViewModel을 활용한 MVVM 아키텍처 패턴 구현

<br>

## 참고자료

<https://wonsohana.wordpress.com/2019/05/14/android-mvvm-%EC%9D%84-%EC%A0%95%EB%A6%AC%ED%95%B4%EB%B3%B4%EC%9E%90/>  
<https://blog.yena.io/studynote/2019/03/16/Android-MVVM-AAC-1.html>

<br>

*Writer: Jae-Hwan Lee*
