---
layout: post
title: Android - GestureDetector를 이용한 좌우 스와이프 터치리스너 (Kotlin)
feature-img: "assets/img/pexels/computer.jpeg"
tags: [Kotlin, 코틀린, JAVA, Android, GestureDetector]
author: Jae-Hwan Lee
excerpt_separator: <!--more-->
---

# Android - GestureDetector를 이용한 좌우 스와이프 터치리스너 (Kotlin)
<!--more-->
* **연구주제** : Android - GestureDetector를 이용한 좌우 스와이프 터치리스너 (Kotlin)
* **연구목적** : 안드로이드에서의 코틀린 활용
* **연구일시** : 2020년 03월 19일 09:00~17:00
* **연구자** : 이재환 <ljh951103@naver.com>
* **연구장비** : HP EliteDesk 800 G4 TWR, Kotlin, Android studio, IntelliJ
* **관련연구** : Java, Android, Kotlin, GestureDetector

<br>

## 서론

안드로이드에서 앱을 개발할 떄, 좌우 스와이프에 대한 구현은 대부분 뷰페이저로 구현한다.  
하지만 간혹, 뷰페이저를 사용하지않고 스와이프 이벤트를 받아서 효과를 출력하고자하는 경우가 있다.  
그럴 때에는  `GestureDetector`를 이용하여 제스처를 감지하고, 그에 맞는 제스처가 발생했을 때의 동작을 정의할 수 있다.

<br>

## 본론

### **제스처 클래스**

`GestureDetector.OnGestureListener` 를 구현하는 클래스를 작성한다.  
좌/우 스와이프만 다룰 것이기 때문에 onFling 함수를 재정의한다.


````
class SwipeGesture(v: View) : GestureDetector.OnGestureListener {
    private val SWIPE_THRESHOLD = 100
    private val SWIPE_VELOCITY_THRESHOLD = 100
    val v = v
    val month_left_button = v.findViewById<AppCompatImageButton>(R.id.cal_month_left)
    val month_right_button = v.findViewById<AppCompatImageButton>(R.id.cal_month_right)
    // 제스처 이벤트를 받아서 변경
    override fun onFling(e1: MotionEvent?, e2: MotionEvent?, velocityX: Float, velocityY: Float): Boolean {
        var result = false
        try {
            val diffY = e2!!.y - e1!!.y
            val diffX = e2.x - e1.x
            if (Math.abs(diffX) > Math.abs(diffY)) {
                if (Math.abs(diffX) > SWIPE_THRESHOLD && Math.abs(velocityX) > SWIPE_VELOCITY_THRESHOLD) {
                    if (diffX > 0) {
                        onSwipeRight()

                    } else {
                        onSwipeLeft()
                    }
                }
                result = true
            }
            result = true
        } catch (exception: Exception) {
            exception.printStackTrace()
        }
        return result
    }

    fun onSwipeRight() {
        val month_left_button = v.findViewById<AppCompatImageButton>(R.id.cal_month_left)
        month_left_button.performClick()
    }

    fun onSwipeLeft() {
        val month_right_button = v.findViewById<AppCompatImageButton>(R.id.cal_month_right)
        month_right_button.performClick()
    }

    override fun onShowPress(e: MotionEvent?) {
    }
    override fun onSingleTapUp(e: MotionEvent?): Boolean {
        return true
    }
    override fun onDown(e: MotionEvent?): Boolean {
        return true
    }
    override fun onScroll(e1: MotionEvent?, e2: MotionEvent?, distanceX: Float, distanceY: Float): Boolean {
        return true
    }
    override fun onLongPress(e: MotionEvent?) {
    }

}
````

<br>

### **리스너***

다음과 같이 람다식으로 이벤트에 대한 동작을 달아준다.

````
val gestureListener: SwipeGesture = SwipeGesture(calendar_allheader)
        val gesturedetector = GestureDetector(calendar_allheader.context, gestureListener)
        calendar_allheader.setOnTouchListener { v, event ->
            return@setOnTouchListener gesturedetector.onTouchEvent(event)
````

<br>

## 결론

사용자의 제스처를 감지해서 터치이벤트로 스와이프가 정상적으로 잘 동작함을 확인할 수 있다.  
여기서 한 가지 주의사항을 말하자면, 만약 해당 뷰가 최상위 객체가 아닐 경우, 제대로 동작하지않는다.  
이건 클릭리스너나 다른 리스너에도 공통되는 것으로 주의하길 바란다.

<br>

## 향후과제

<br>

## 참고자료

<https://ohkani.tistory.com/entry/%EC%95%88%EB%93%9C%EB%A1%9C%EC%9D%B4%EB%93%9C-%ED%84%B0%EC%B9%98%ED%99%94%EB%A9%B4-%EC%A0%9C%EC%8A%A4%EC%B2%98-%EA%B8%B0%EB%8A%A5%EC%9D%84-%EC%9D%B4%EC%9A%A9%ED%95%9C-%ED%84%B0%EC%B9%98-%EC%9D%B8%EC%8B%9D>

<br>

*Writer: Jae-Hwan Lee*
