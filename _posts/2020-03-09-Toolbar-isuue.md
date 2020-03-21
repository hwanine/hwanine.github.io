---
toc: true
toc_sticky: true
categories:
  - Android
title: Android - Toolbar, Fragment 문제 및 해결 (Kotlin)
tags: [Kotlin, 코틀린, JAVA, Android, Toolbar]
excerpt: "Toolbar, Fragment 문제 및 해결방법"
---

* **연구주제** : Android - Toolbar, Fragment 문제 및 해결 (Kotlin)
* **연구목적** : 안드로이드에서의 코틀린 활용
* **연구일시** : 2020년 03월 09일 09:00~17:00
* **연구자** : 이재환 <ljh951103@naver.com>
* **연구장비** : HP EliteDesk 800 G4 TWR, Kotlin, Android studio, IntelliJ
* **관련연구** : Java, Android, Kotlin, Toolbar

<br>
   
## 서론

개발을 하던 중, 문제가 발생했다.  
우선 프로그램의 화면은 AppBarLayout에 toolbar를 사용하고 5개의 프래그먼트들로 이루어져 있다.  
여기서 툴바에는 `scrollfrags` 설정이 되어있어 스크롤 시, 툴바가 숨겨진다.  
즉, 리사이클러뷰를 사용할 경우 AppBarLayout이 숨겨지는데 이 때, 바텀 내비게이션뷰를 통해 페이지를 이동하여도 툴바가 숨긴채로 그대로 유지되는 현상이 발생한다. 

특히나, 만약 리사이클러뷰로 구성하지않고 사용자 커스텀으로 구성한 프래그먼트의 경우는 문제가 크게 발생한다.  
툴바를 사용하지않을 경우에는 그 프래그먼트의 툴바에 `visible` 메소드를 통해 숨겨주어야한다.  
이렇게 하면 해당 프래그먼트로 이동했을 떄, 툴바가 숨겨지기도 하지만 다른 프래그먼트에서 툴바가 사라진 채로 해당 프래그먼트로 이동을 하게되면 툴바 뿐만아니라 툴바의 높이만큼 뷰가 사라져 있는 것이다.  

![image](https://user-images.githubusercontent.com/57826388/76138947-3391bb80-608f-11ea-82b7-49ace8f10051.png)


<br>
   
## 본론

이 문제를 해결하기위해 안드로이드 개발 페이지나 스택오버플로우, 구글 검색을 통해 끝없이 찾아보았지만 나오지않았다.  
직접 툴바 클래스의 메소드를 찾아보고 시도해봐도 여전했다.  
하지만 꾸준히 찾아본 결과, 해결 방안을 찾아내서 공유하고자 한다.

`setExpanded()`

이 메소드를 이용하면 된다.  
이것은 툴바를 확장시켜주는 메소드다.  
주의사항으로는 툴바가 아닌 앱바의 메소드기 떄문에 툴바보다 상위 클래스인 앱바 인스턴스로 호출해야한다!

````kotlin
ab.setExpanded(true, false)
````

다음과 같이 입력하면 앱바가 사라진 화면에서 툴바가 자동으로 내려온다.  
인자는 Boolean 두 가지를 받는데 다음과 같다.


1. 확장할건지의 여부

2. smooth하게 동작할지의 여부(애니메이션)

<br>

## 결론

이렇게 앱바레이아웃의 setExpanded 메소드를 통해 사용자가 직접 확장할 수 있다.  
자료가 별로 없었기 때문에 찾느라 굉장히 힘들었던 만큼, 오랫동안 기억할 수 있을 것 같다.

<br>

## 향후과제

<br>

## 참고자료

<https://saviorj.tistory.com/21?category=683054>  

<br>

*Writer: Jae-Hwan Lee*
