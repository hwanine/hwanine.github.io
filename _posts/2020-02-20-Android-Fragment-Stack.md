---
layout: post
title: Android - 액티비티와 프래그먼트의 생명주기 (Kotlin)
feature-img: "assets/img/pexels/computer.jpeg"
thumbnail: "https://user-images.githubusercontent.com/57826388/74733647-1340bf00-5290-11ea-9516-a542d9fc9ca5.png
"
tags: [Kotlin, 코틀린, JAVA, Android, Activity, Fragment]
author: Jae-Hwan Lee
excerpt_separator: <!--more-->
---

# Android - 액티비티와 프래그먼트 생명주기 (Kotlin)
<!--more-->
* **연구주제** : 액티비티와 프래그먼트 생명주기 (Kotlin)
* **연구목적** : 안드로이드에서의 코틀린 활용
* **연구일시** : 2020년 02월 20일 09:00~17:00
* **연구자** : 이재환 <ljh951103@naver.com>
* **연구장비** : HP EliteDesk 800 G4 TWR, Kotlin, Android studio, IntelliJ
* **관련연구** : Java, Android, Kotlin, Activity, Fragment

<br>
   
## 서론

이번 시간에는 액티비티와 프래그먼트의 생명주기에 대해 알아보겠다.  
사실 저번 포스팅에서 내용을 다뤘으나 본격적으로 들어가기 전에 간단하게 나마 프래그먼트에 대해 설명하겠다.  

프래그먼트는 기술의 발전과 디바이스의 다양화로 인한 문제의 환경속에서 새로운 개발방법으로 소개된 것이다.

![image](https://user-images.githubusercontent.com/57826388/74733647-1340bf00-5290-11ea-9516-a542d9fc9ca5.png)

위의 사진처럼 다양한 디바이스 환경에서 액티비티를 새로 만들기 보다는프래그먼트만 재사용하게 되면 간편하게 UI를 구성할 수 있다.

### **프래그먼트의 특징**

- 액티비티를 분할하여 화면의 한 부분을 정의

- 액티비티와 같이 레이아웃, 동작처리, 생명주기를 가지는 독립적인 모듈

- 다른 액티비티에서도 사용할 수 있다.(재사용)

- 액티비티 내에서 실행 중에 추가, 제거가 가능하다.

***결국 프래그먼트는 액티비티 안에서 UI를 정의할 수 있는 더 작은 단위이다.***

즉, 다수의 액티비티에서 하나의 프래그먼트를 사용할 수 있고, 하나의 액티비티에서 다수의 프래그먼트를 사용할 수도 있다는 것이다.

<br>
   
## 본론

### **생명주기**

생명주기란 무엇일까? 생명주기는 영어로 LifeCycle이다.  
즉, 프래그먼트를 예로들면 하나의 프래그먼트가 생성되고 종료되는 주기를 말한다.

프래그먼트의 상태저장은 액티비티와 마찬가지로 bundle을 이용하여 onSaveInstanceState에 저장된다.

프래그먼트를 삭제하거나 상태를 되돌리는 등의 연산을 할 경우에는 onCreate(), onCreateView(), onActivityCreated() 등의 함수 안에서 상황에따라 상태를 되돌리면 된다.

<br>

### ***프래그먼트와 액티비티의 생애주기 차이점***

> 프래그먼트

- 백스택에 저장되고 상태를 유지해야함.
- 트랜잭션을 통해 addToBackStack() 함수를 통해 백스택으로의 저장을 요청해야함

> 액티비티

- 시스템에서 자동적으로 백스택에 저장됨.

<br>

### ***액티비티 생명주기***

![image](https://user-images.githubusercontent.com/57826388/74731301-0ec5d780-528b-11ea-8b63-62601844c554.png)

- onCreate()
    - Activity가 처음 생성될 때 호출
    - savedInstanceState를 통해 이전 상태를 불러오거나, Activity에 필요한 구성 요소들을 초기화하며, UI 레이아웃을 정의
    - 저장되었던 데이터에 의해 화면을 이전상태로 복원

- onRestart()
    - Activity가 중단되었다가 다시 시작되기 전에 호출

- onStart()
    - Activity가 화면에 보이기 바로 직전에 호출

- onResume()
    - Activity가 사용자와 상호작용을 하기 바로 직전에 호출
    - 이 단계 이후, 우리가 사용 가능한 Activity가 실행 되게 된다.

- onPause()
    - Activity가 다른 Activity로 가려져 화면에서 사라지기 시작할 때 호출
    - 사용자가 필요하지 않는 리소스 등을 해제 할 수 있다.

- onStop()
    - Activity가 종료되거나, 다른 Activity가 화면을 가릴 졌을 경우 등의 상황에 호출
    - Activity Object가 메모리에만 남아있게 된다. 여기서는 자동으로 onSaveInstanceState()가 실행되어, 데이터를 Key-value 형태로 값을 저장하여 onCreate()에서 다시 복원을 하여 데이터만 있다면 화면을 다시 불러올 수 있다.

- onDestroy()
    - Activity가 완전히 소멸될 때 호출

<br>

### ***프래그먼트 생명주기***

![image](https://user-images.githubusercontent.com/57826388/74737095-784be300-5297-11ea-8e41-b87bde0d96d7.png)

- onAttach()
    - Fragment가 Activity에 연결될 때 호출

- onCreate()
    - Fragment가 처음 생성될 때 호출
    - Activity와 마찬가지로 리소스들을 초기화
    - 생성시 넘겨받은 값들이 있다면 여기서 변수에 넣어주면 된다.

- onCreateView()
    - Fragment의 layout을 inflate하여 View 객체를 얻을 수 있음 

- onActivityCreated()
    - Activity에서 Fragment를 모두 생성된 후에 호출
    - UI에 관련된 View를 변경하는 작업이 가능

- onStart()   
    - Activity와 마찬가지로 Fragment가 화면에 보이기 바로 직전에 호출

- onResume()   
    - 마찬가지로, Fragment가 사용자와 상호작용을 하기 바로 직전에 호출

- onPause()
    - 다른 Fragment가 화면을 가리게 되어 기존 Fragment의 화면이 사라지게 되는 시점에서 호출, 기존 레이아웃은 백스택으로 들어감.

- onStop()
    - Fragment가 화면에서 더이상 보여지지 않게 되었을 때 호출
    - 기능을 완전히 상실함을 의미

- onDestroyView()
    - Fragment의 View들의 리소스를 해제되는 구간

- onDestroy(
    - Fragment가 완전히 소멸될 때 호출

- onDetach()
    - Fragment가 Activity와 연결이 끊기기 직전에 호출

<br>
   
## 결론

지금까지 생애주기에 대해 알아보았다.  
액티비티의 생애주기와 프래그먼트의 생애주기는 전체적으로 비슷하지만 역시나 큰 차이는 액티비티는 백스택에 `자동적`으로 저장되지만 프래그먼트는 `수동적`이라는 점이다.

<br>

## 향후과제

- 백 버튼 내비게이션 뷰 아이템 활성화

<br>

## 참고자료

<https://re-build.tistory.com/4>  
<https://ifcontinue.tistory.com/14>

<br>

*Writer: Jae-Hwan Lee*