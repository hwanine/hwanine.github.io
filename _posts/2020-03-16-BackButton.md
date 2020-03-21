---
toc: true
toc_sticky: true
categories:
  - Android
title: Android - 프래그먼트에서 뒤로가기 두번 눌러서 앱 종료하기 (Kotlin)
tags: [Kotlin, 코틀린, JAVA, Android, Fragment, Backbutton]
excerpt: "안드로이드에서 프래그먼트에서 뒤로가기 두번 눌러서 앱 종료하기"
---

* **연구주제** : Android - 프래그먼트에서 뒤로가기 두번 눌러서 앱 종료하기 (Kotlin)
* **연구목적** : 안드로이드에서의 코틀린 활용
* **연구일시** : 2020년 03월 16일 09:00~17:00
* **연구자** : 이재환 <ljh951103@naver.com>
* **연구장비** : HP EliteDesk 800 G4 TWR, Kotlin, Android studio, IntelliJ
* **관련연구** : Java, Android, Kotlin, Fragment, Backbutton

<br>

## 서론

앱을 사용하다보면 뒤로가기 두번을 누르는 경우, 앱이 종료되게 되어있는 앱들을 많이 발견할 수 있다.  
프래그먼트의 경우는 일반 로직과 살짝 다르다.  
뒤로가기 버튼을 눌러도 프래그먼트 백로그에 데이터가 남아있다면 종료해서는 안된다.  
이번 포스팅에서는 프래그먼트에서 뒤로가기 두번 눌러서 앱 종료하게하는 부분을 다루겠다.

<br>

## 본론
   
````kotlin
private final var FINISH_INTERVAL_TIME: Long = 2000
private var backPressedTime: Long = 0
````

우선 누르는 이벤트사이의 시간 간격을 위해 FINISH_INTERVAL_TIME을 지정해준다.  
설정된 시간 이내에 연속적으로 뒤로가기가 이벤트가 일어났을때만 종료 되게 된다.  
설정해 놓은 시간이 지나면 다시 두번 눌러야 종료되는 상태로 초기화 된다.  
backPressedTime은 첫번째 뒤로가기 버튼이 눌린 시간을 저장한다.  

<br>

````kotlin
 override fun onBackPressed() {
        if(supportFragmentManager.backStackEntryCount == 0) {
            var tempTime = System.currentTimeMillis();
            var intervalTime = tempTime - backPressedTime;
            if (0 <= intervalTime && FINISH_INTERVAL_TIME >= intervalTime) {
                super.onBackPressed();
            } else {
                backPressedTime = tempTime;
                Toast.makeText(this, "'뒤로' 버튼을 한 번 더 누르면 종료됩니다.", Toast.LENGTH_SHORT).show();
                return
            }
        }
        super.onBackPressed();
    }
````

- `if(supportFragmentManager.backStackEntryCount == 0)`: 백로그가 남아있는지 안남아있는지 검사한다. 비어있을 경우, 실행한다.

다음과 같이 백로그가 비어있을 경우, 뒤로가기를 누르면 토스트 메시지가 출력된다.  
시간을 계속 체크하여 처음 누른 시간과 후에 누른시간을 계속 비교한다.  
2초 이내일 경우, 앱을 종료하고 2초 이상이면 앱을 유지한다.

<br>
   
## 결론

![image](https://user-images.githubusercontent.com/57826388/76140402-7490cc80-609d-11ea-8eda-d43fc1720f05.png)

프래그먼트 백로그에 데이터가 많이 쌓이더라도 조건문을 통해 백로그가 없을 경우만 동작하며, 종료되기까지 정상적으로 잘 동작됨을 알 수 있다.

<br>

## 향후과제

<br>

## 참고자료

<http://devstory.ibksplatform.com/2017/11/android.html>  

<br>

*Writer: Jae-Hwan Lee*