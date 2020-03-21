---
toc: true
toc_sticky: true
categories:
  - Android
title: Android - 리사이클러뷰에서 최상단, 최하단으로 이동하기 (Kotlin)
tags: [Kotlin, 코틀린, JAVA, Android, RecyclerView]
excerpt_separator: <!--more-->
---

# Android - 리사이클러뷰에서 최상단, 최하단으로 이동하기 (Kotlin)
<!--more-->
* **연구주제** : Android - 리사이클러뷰에서 최상단, 최하단으로 이동하기 (Kotlin)
* **연구목적** : 안드로이드에서의 코틀린 활용
* **연구일시** : 2020년 03월 17일 09:00~17:00
* **연구자** : 이재환 <ljh951103@naver.com>
* **연구장비** : HP EliteDesk 800 G4 TWR, Kotlin, Android studio, IntelliJ
* **관련연구** : Java, Android, Kotlin, RecyclerView

<br>

## 서론

흔히 스크롤이 있는 화면에서 앱 최상단으로 이동한다거나, 최하단으로 이동하는 버튼이 존재하고있음을 확인할 수 있다.  
한 뷰에 속하는 데이터가 많을 경우, 일일이 스크롤을 통해 해당 데이터를 찾기가 힘들기 때문이다.  
따라서 이번에는 이 두가지 버튼을 생성해보고자 한다.

<br>

## 본론

### **레이아웃**

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
        android:alpha="0.3"
        app:layout_behavior="com.google.android.material.behavior.HideBottomViewOnScrollBehavior"/>

    <ImageView
        android:id="@+id/down_button"
        android:layout_width="40dp"
        android:layout_height="40dp"
        android:layout_gravity="end|bottom"
        android:src="@drawable/ic_down"
        android:layout_margin="10dp"
        android:alpha="0.3"
        app:layout_behavior="com.google.android.material.behavior.HideBottomViewOnScrollBehavior"/>

</androidx.coordinatorlayout.widget.CoordinatorLayout>
````

다음과 같이 입력하면 레이아웃은 다음 화면을 출력한다.

![image](https://user-images.githubusercontent.com/57826388/76140589-73f93580-609f-11ea-96bf-daf35e451b37.png)

<br>

### **버튼 리스너**

````kotlin
 fun updown_Listener(view: RecyclerView?) {
        up_button.setOnClickListener {
            view?.smoothScrollToPosition(0)
        }

        down_button.setOnClickListener {
            view?.smoothScrollToPosition(PhotoList.size)
        }
    }
````

다음과 같이 버튼에 클릭 리스너를 달아준다.  
up_button의 경우 맨 위로 이동하게되고, down_botton은 데이터 리스트의 크기만큼의 position을 할당받고 스크롤이 내려가게된다.  

만약 스크롤 애니메이션을 사용하지 않을 경우, smooth를 제거한 ScrollToPosition() 메소드가 존재하니 이것을 이용하면 된다.

<br>
   
## 결론

![image](https://user-images.githubusercontent.com/57826388/76140626-ee29ba00-609f-11ea-9792-86342d09170f.png)

다음과 같이 버튼이 출력되어 버튼을 각각 클릭할 경우, 최상단 및 최하단으로 스크롤이됨을 볼 수 있다.

<br>

## 향후과제

<br>

## 참고자료

<br>

*Writer: Jae-Hwan Lee*