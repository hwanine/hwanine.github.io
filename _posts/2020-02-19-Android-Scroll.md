---
toc: true
toc_sticky: true
categories:
  - Android
title: Android - Scroll시 상, 하단바 숨기기 (Kotlin)
tags: [Kotlin, 코틀린, JAVA, Android, CoordinatorLayout]
excerpt: "Scroll시 상, 하단바 숨기기 (Kotlin)"
---

* **연구주제** : Scroll시 상, 하단바 숨기기 (Kotlin)
* **연구목적** : 안드로이드에서의 코틀린 활용
* **연구일시** : 2020년 02월 19일 09:00~17:00
* **연구자** : 이재환 <ljh951103@naver.com>
* **연구장비** : HP EliteDesk 800 G4 TWR, Kotlin, Android studio, IntelliJ
* **관련연구** : Java, Android, Kotlin,  CoordinatorLayout

<br>
   
## 서론

우리가 유튜브 같은 앱들을 사용하다보면 스크롤 할 때마다, 상단에 검색툴바나 하단의 바가 사라지는 경우도 있고, 또 갑자기 나타나는 경우도 있다.  
이번 포스팅에서는 이러한 효과에 대해 알고 실제로 구현해본다.

<br>

### **CoordinatorLayout**

이러한 효과를 구현하기 위해서는 `CoordinatorLayout`을 사용해야한다.  
CoordinatorLayout은 막강한 FrameLayout이라고 할 수 있다.  
Behavior을 통해 자식 뷰들 간에 다양한 상호작용을 제공한다.  

이것을 이용하면 자식 뷰들을 드래그해서 움직이는 것도 가능하고 패널들을 스와이프하여 지우기, 애니메이션 적용 등 다양한 기능을 활용할 수 있다.  
우리는 다른 부분보다 스크롤 이벤트에 대해 조금 더 초점을 맞추기로 하자.

<br>

### **AppBarLayout**

`AppBarLayout`은 LinearLayout을 상속한 레이아웃으로 스크롤 제스처에 몇 가지 동작을 적용할 수 있다.  
이 레이아웃은 자식으로 ToolBar를 지니고 있다.

***주의할 점은 AppBarLayout은 반드시 CoordinatorLayout의 바로 아래 자식이어야한다.***

또한 스크롤을 인식하기 위해서는 형제로 선언된 뷰가 RecyclerView 같은 스크롤 가능한 뷰여야만 한다.  
우리는 FrameLayout에서 각 프래그먼트의 RecyclerView를 사용한다

<br>

### **BottomNavigationView**

바텀 내비게이션 뷰는 저번 시간에 설명했기 때문에 생략하고 지나가도록 하겠다.

<br>
   
## 본론

### **build.gradle**

````kotlin
dependencies {
    implementation 'com.google.android.material:material:1.0.0'
}
````

우선 모듈 수준의 build.gradle에 머터리얼 디자인 패키지를 추가한다.  

<br>

### **values/styles.xml**

````xml
<style name="AppTheme" parent="Theme.MaterialComponents.Light.NoActionBar">
        <!-- Customize your theme here. -->
        <item name="colorPrimary">@color/Select</item>
        <item name="colorPrimaryDark">@color/Selected</item>
        <item name="colorAccent">@color/colorAccent</item>
        <item name="windowActionBar">false</item>
        <item name="windowNoTitle">true</item>
    </style>
````

액션바 디자인을 설정한다.  
여기서 우리의 코드가 NoActionBar를 기준으로 작성되었기 때문에 이것을 입력한다.

<br>

### **main_activity.xml**

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
    <FrameLayout
        android:id="@+id/frame_layout"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        app:layout_behavior="@string/appbar_scrolling_view_behavior"/>   // 프레임에 behavior값 적용


    <!-- 상단바 -->
    <com.google.android.material.appbar.AppBarLayout
        android:id="@+id/appbar"
        android:layout_width="match_parent"
        android:layout_height="wrap_content">

        <androidx.appcompat.widget.Toolbar
            android:id="@+id/main_toolbar"
            android:layout_width="match_parent"
            android:layout_height="?attr/actionBarSize"
            android:background="?attr/colorPrimary"
            android:theme="@style/AppTheme"
            app:layout_scrollFlags="scroll|enterAlways"   // 상단바 숨기기
            app:contentInsetStart="0dp"
            >

            <ImageView
                android:layout_width="?attr/actionBarSize"
                android:layout_height="?attr/actionBarSize"
                android:padding="5dp"
                android:src="@drawable/toolbar_image" />

            <androidx.appcompat.widget.AppCompatSpinner
                android:layout_width="wrap_content"
                android:layout_height="match_parent"
                android:entries="@array/search_option_array" />

            <androidx.appcompat.widget.SearchView
                android:id="@+id/search"
                android:layout_width="match_parent"
                android:layout_height="match_parent" />
        </androidx.appcompat.widget.Toolbar>
    </com.google.android.material.appbar.AppBarLayout>


    <!-- 하단바 -->
    <com.google.android.material.bottomnavigation.BottomNavigationView
        android:id="@+id/bottomNavigationView"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_gravity="bottom"
        android:layout_marginStart="0dp"
        android:layout_marginEnd="0dp"
        android:background="#EE88C9"
        app:itemBackground="@color/cardview_light_background"
        app:itemTextColor="@drawable/bottombar_check"
        app:itemIconTint="@drawable/bottombar_check"
        app:labelVisibilityMode="labeled"
        app:menu="@menu/menu_bottom"
        app:layout_behavior="com.google.android.material.behavior. //   하단바 숨기기 HideBottomViewOnScrollBehavior" />

</androidx.coordinatorlayout.widget.CoordinatorLayout>
````

우리는 상하단바를 모두 숨길 것이기 때문에 다음과 같이 작성한다.  
상단바의 경우에는 스타일에 값을 지정해주었는데 직접 레이아웃에 작성해도 무방하다.

<br>

### **values/strings.xml**

````xml
<string name="appbar_scrolling_view_behavior" translatable="false">
        com.google.android.material.appbar.AppBarLayout$ScrollingViewBehavior
    </string>
````

<br>

> **app:layout_scrollFlag**

툴바에서 `scrollFralgs`를 이용하여 다음과 같이 스크롤 옵션을 지정할 수 있다.  

- scroll: 스크롤 이벤트 적용

- snap: View가 부분적으로만 표시되면, View가 스냅되어 가장 가까운 가장자리로 스크롤

- enterAlways:	아래로 스크롤 할 때마다 이 View가 아래로 보여짐

- enterAlwaysCollapsed: enterAlways 속성과 비슷하지만 스크롤 뷰가 맨 위에 도달했을 때 전체뷰가 보여짐

- exitUntilCollapsed: 	스크롤을 아래, 위로 이동 시 View의 minHeight 만큼만 보여지고 스크롤이 최상단에 도착 시 나머지 View의 전체가 보여짐

준비는 다 되었다. 데이터셋이나 메인액티비티 클래스를 보고싶으면 지난 포스팅을 참조하길 바란다. 그럼 실행해보자.

<br>

## 결론

<iframe width="1280" height="753" src="https://www.youtube.com/embed/EB4Thf8MJE0" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

<br>

위의 영상에서 보듯 스크롤에 따라 상단 툴바와 바텀 내비게이션 뷰가 사라지기도 하고 나타나기도 한다.  
유튜브 앱처럼 결과가 잘 나온 것을 확인할 수 있다.

<br>

## 향후과제

- 프래그먼트 생명주기 설정 
- 백 버튼 내비게이션 뷰 아이템 활성화

<br>

## 참고자료

<https://readystory.tistory.com/127>  

<br>

*Writer: Jae-Hwan Lee*