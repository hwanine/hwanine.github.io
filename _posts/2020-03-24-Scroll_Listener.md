---
toc: true
toc_sticky: true
categories:
  - Android
title: Android - RecyclerView에서 스크롤 할 때마다 특정 뷰 숨기기 (Kotlin)
tags: [Kotlin, 코틀린, JAVA, Android, RecyclerView, Scroll]
excerpt_separator: <!--more-->
---

# Android - RecyclerView에서 스크롤 할 때마다 특정 뷰 숨기기 (Kotlin)
<!--more-->
* **연구주제** : Android - RecyclerView에서 스크롤 할 때마다 특정 뷰 숨기기 (Kotlin)
* **연구목적** : 안드로이드에서의 코틀린 활용
* **연구일시** : 2020년 03월 24일 09:00~17:00
* **연구자** : 이재환 <ljh951103@naver.com>
* **연구장비** : HP EliteDesk 800 G4 TWR, Kotlin, Android studio, IntelliJ
* **관련연구** : Java, Android, Kotlin, RecyclerView, Scroll

<br>

## 서론

웹이나 스크롤이 있는 앱의 뷰를 사용할 때, 그 뷰에는 수 많은 아이템이 포함되어있다.  
그래서 뷰를 스크롤 해보면 숨겨져있던 맨 위로 이동하는 버튼이 나타나곤한다.  

이렇듯 리사이클러뷰에서 스크롤 할 때, 업/다운 버튼을 숨기고 다시 스크롤을 멈추면 업/다운 버튼을 나타나게 해보도록 해보자.

<br>

## 본론

### **레이아웃**

우선 표시할 버튼에 대한 레이아웃을 구성한다.  
본 예제에서는 상단바, 프레임으로 레이아웃이 이루어진다.  
버튼은 프레임 위에 나타나게된다.

````xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.coordinatorlayout.widget.CoordinatorLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">

    <!-- 프레임 -->
    <androidx.recyclerview.widget.RecyclerView
        android:id="@+id/photo_recyclerView"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        app:layout_behavior="@string/appbar_scrolling_view_behavior"/>


    <!-- 상단바 -->
    <com.google.android.material.appbar.AppBarLayout
        android:id="@+id/appbar2"
        android:layout_width="match_parent"
        android:layout_height="wrap_content">

        <androidx.appcompat.widget.Toolbar
            android:id="@+id/photo_toolbar"
            android:layout_width="match_parent"
            android:layout_height="@dimen/top_size"
            android:background="@color/bar"
            android:minHeight="@dimen/top_size"
            android:theme="@style/AppTheme"
            app:contentInsetStart="0dp"
            app:layout_scrollFlags="scroll|enterAlways">

            <ImageView
                android:id="@+id/title_type"
                android:layout_width="@dimen/top_size"
                android:layout_height="@dimen/top_size"
                android:layout_marginLeft="3dp"
                android:layout_marginRight="5dp"
                android:padding="5dp" />

            <androidx.appcompat.widget.AppCompatTextView
                android:id="@+id/title_name"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:textSize="22dp" />


        </androidx.appcompat.widget.Toolbar>
    </com.google.android.material.appbar.AppBarLayout>



    <ImageView
        android:id="@+id/up_button"
        android:layout_width="40dp"
        android:layout_height="40dp"
        android:layout_gravity="start|bottom"
        android:src="@drawable/ic_up"
        android:layout_margin="10dp"
        android:alpha="0.25" />

    <ImageView
        android:id="@+id/down_button"
        android:layout_width="40dp"
        android:layout_height="40dp"
        android:layout_gravity="end|bottom"
        android:src="@drawable/ic_down"
        android:layout_margin="10dp"
        android:alpha="0.25" />

</androidx.coordinatorlayout.widget.CoordinatorLayout>
````

<br>

### **리스너 구현 **

스크롤 리스너를 재정의한 객체를 생성해준다.  

`onScrolled` 는 스크롤이 되는 중일 때 호출된다.  
`onScrollStateChanged` 는 스크롤이 끝났을 때, 호출된다.

예제에서 temp를 준 이유는 처음 뷰에 들어오면 알아서 onScrolled가 호출됨을 확인했다.  
그로 인해, 스크롤 하면 버튼이 나타났다가 다시 사라기 때문에 처음 들어왔을 때, temp를 주어 관리한다.

````kotlin
val onScrollListener = object:RecyclerView.OnScrollListener() {
            var temp: Int = 0
            override fun onScrolled(@NonNull recyclerView:RecyclerView, dx:Int, dy:Int) {
                if(temp == 1) {
                    super.onScrolled(recyclerView, dx, dy)
                    up_button.visibility = View.GONE
                    down_button.visibility = View.GONE
                }
            }
            override fun onScrollStateChanged(recyclerView: RecyclerView, newState: Int) {
                super.onScrollStateChanged(recyclerView, newState)
                up_button.visibility = View.VISIBLE
                down_button.visibility = View.VISIBLE
                temp = 1
            }
        }
    
````

<br>

### **리스너 - 뷰 연결**

다음과 같이 리스너와 뷰를 연결해준다.

````kotlin
recyclerView?.setOnScrollListener(onScrollListener)
````

<br>

## 결론

다음과 같이 스크롤을 하지 않을 때는 버튼이 나타나지만

![image](https://user-images.githubusercontent.com/57826388/76158005-58039b80-6154-11ea-9ec8-97092c2a6c36.png)

<br>

스크롤을 하는 중에는 버튼이 사라지게 된다.

![image](https://user-images.githubusercontent.com/57826388/76158007-5df97c80-6154-11ea-8365-35680e894288.png)

<br>

## 향후과제

<br>

## 참고자료

<br>

*Writer: Jae-Hwan Lee*