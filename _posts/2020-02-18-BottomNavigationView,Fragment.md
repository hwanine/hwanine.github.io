---
toc: true
toc_sticky: true
categories:
  - Android
title: Android - BottomNavigationView 및 Fragment 관리 (Kotlin)
tags: [Kotlin, 코틀린, JAVA, Android, IntelliJ, Fragment, BottomNavigationView]
excerpt: "안드로이드에서 BottomNavigationView 및 Fragment 활용"
---

* **연구주제** : 안드로이드에서 BottomNavigationView 및 Fragment 활용
* **연구목적** : 안드로이드에서의 코틀린 활용
* **연구일시** : 2020년 02월 18일 09:00~17:00
* **연구자** : 이재환 <ljh951103@naver.com>
* **연구장비** : HP EliteDesk 800 G4 TWR, Kotlin, Android studio, IntelliJ
* **관련연구** : Java, Android, Kotlin, IntelliJ, Fragment, BottomNavigationView

<br>
   
## 서론

이번 포스팅에서는 프래그먼트, 리사이클러뷰, 툴바, 바텀 내비게이션 뷰를 이용하여 뷰를 구성해볼 것이다.  
그럼 각각의 개념부터 알아보자.

### **프래그먼트**

프래그먼트는 무엇일까? 프래그먼트는 UI의 기본이 되는 요소인 Activity와 비슷하면서 약간 다르다.  
액티비티의 경우, 한 화면에 여러 자식 View를 그리면서 화면을 구성한다.  

점점 기술이 발달하면서 여러 크기를 가진 디바이스가 나오면서, 또 앱 구조가 복잡해지면서 프래그먼트가 도입되었다.   
프래그먼트는 하나의 액티비티가 여러 개의 화면을 가지도록 만들기위해 고안된 개념이다.  
이러한 프래그먼트는 액티비티 내의 UI 구성을 여러 개의 모듈 단위로 작성할 수 있게 해준다. 따라서 한번 작성된 프래그먼트는 여러 액티비티에서 재사용이 가능하므로 효율성이 증가되었다.

이 프래그먼트의 특징은 다음과 같다.

- 액티비티를 분할하여 화면의 한 부분을 정의

- 액티비티와 같이 레이아웃, 동작처리, 생명주기를 가지는 독립적인 모듈

- 다른 액티비티에서도 사용할 수 있다.(재사용)

- 액티비티 내에서 실행 중에 추가, 제거가 가능하다.

우리는 이 프래그먼트로 각각의 다섯개의 프래그먼트 화면을 구성해볼 것이다.

<br>

### **리사이클러 뷰**

리사이클러뷰는 지난 포스팅에서 설명했다. 프래그먼트 레이아웃마다 재사용하기 위해 사용한다.

<br>

### **툴바**

툴바는 안드로이드 상단의 검색창이나 안드로이드 앱에 대한 정보가 나오는 부분을 말한다.  
우리는 다양한 액션들을 제공하는 AppBar를 이용하여 Toolbar를 구성해서 사용할 것이다.

<br>

### **바텀 내비게이션 뷰**

바텀 내비게이션 뷰는 간단하게 안드로이드 하단바를 말한다. TabLayout과 혼동될 수도 있는데 탭 레이아웃은 스와이프를 통해 화면을 넘길 수 있지만 바텀 내비게이션 뷰는 스와이프가 없는 하단바를 말한다.  
지금 당장 유튜브를 켜보아라. 하단에 바가 바로 바텀 내비게이션 뷰이다.

<br>
   
## 본론

이제 실전으로 들어가보자. 우선 설명하기 앞서 라이브러리를 사용하기위한 환경설정, 배경이되는 코드는 언급하지않고 진행하겠다.  
중요 코드부분만 설명하겠다.  
앱의 구조는 다음과 같다.

- 화면구성: 상단 툴바 - 프래그먼트 - 바텀 내비게이션 뷰
- 동작: 바텀 내비게이션 5개의 옵션으로 각각의 프래그먼트를 구성

<br>

### **주 액티비티 레이아웃**

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
        app:layout_behavior="@string/appbar_scrolling_view_behavior"/>


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
            app:layout_scrollFlags="scroll|enterAlways"
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
        app:layout_behavior="com.google.android.material.behavior.HideBottomViewOnScrollBehavior" />

</androidx.coordinatorlayout.widget.CoordinatorLayout>
````

우선 가장 메인이되는 레이아웃이다.   
코드가 상당히 복잡하다. 실제로 개발중인 코드이기 때문에 필요한 기능들이 들어가 있기 때문이다.  
구조만 보면된다.  

전체 프레임, 그리고 그 위에 상단바와 하단바가 위치한다.

이제 우리는 프래그먼트를 구성해야한다.

<br>

### **프래그먼트 / 레이아웃**

````kotlin
import android.os.Bundle
import androidx.fragment.app.Fragment
import android.view.LayoutInflater
import android.view.View
import android.view.ViewGroup

/**
 * A simple [Fragment] subclass.
 */
class LocationFragment : Fragment() {

    override fun onCreateView(
        inflater: LayoutInflater, container: ViewGroup?,
        savedInstanceState: Bundle?
    ): View? {
        // Inflate the layout for this fragment
        return inflater.inflate(R.layout.fragment_location, container, false)
    }


}
````

프래그먼트를 상속받는 클래스를 생성하면 다음과 같다. 다음은 프래그먼트 하나를 의미하기 때문에 우리는 총 5개를 만들어야하니 4개 더 만들어준다.  
레이아웃도 마찬가지로 다음과 같이 5개 만들어주고 프래그먼트에 연결시켜준다.

![image](https://user-images.githubusercontent.com/57826388/74670938-7fb6b200-51ed-11ea-85ac-8440022e1b8e.png)

![image](https://user-images.githubusercontent.com/57826388/74670822-4b42f600-51ed-11ea-846f-3f1c30daab0a.png)


<br>

### **바텀 내비게이션 뷰 - 메뉴**

바텀 내비게이션 뷰에 메뉴를 등록해야한다.  
메뉴는 res - menu - menu_bottom.xml 경로로 파일을 생성한다.

````xml
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto">

    <item
        android:id="@+id/menu_name"
        android:icon="@drawable/ic_folder"
        android:title="이름" />

    <item
        android:id="@+id/menu_tag"
        android:icon="@drawable/ic_tag"
        android:title="특징" />

    <item
        android:id="@+id/menu_cal"
        android:icon="@drawable/ic_cal"
        android:title="날짜" />

    <item
        android:id="@+id/menu_location"
        android:icon="@drawable/ic_location"
        android:title="위치" />

    <item
        android:id="@+id/menu_map"
        android:icon="@drawable/ic_map"
        android:title="지도" />

</menu>
````

작성했으면 바텀 내비게이션 뷰에서 `app:menu="@menu/menu_bottom"` 작성하는 것은 잊으면 안된다.

<br>

### **바텀 내비게이션 뷰, 프래그먼트 연결**

````kotlin
override fun onNavigationItemSelected(p0: MenuItem): Boolean {
        val tb: Toolbar = findViewById(R.id.main_toolbar)
        tb.visibility = View.VISIBLE
        val transaction: FragmentTransaction = supportFragmentManager.beginTransaction()

        when(p0.itemId){
            R.id.menu_name ->{
                val fragmentA = NameFragment()
                transaction.replace(R.id.frame_layout,fragmentA, "name")
            }
            R.id.menu_tag -> {
                val fragmentB = TagFragment()
                transaction.replace(R.id.frame_layout,fragmentB, "tag")
            }
            R.id.menu_cal -> {
                val fragmentC = CalFragment()
                transaction.replace(R.id.frame_layout,fragmentC, "cal")
            }
            R.id.menu_location -> {
                val fragmentD = LocationFragment()
                transaction.replace(R.id.frame_layout,fragmentD, "location")
            }
            R.id.menu_map -> {
                val fragmentE = MapFragment()
                transaction.replace(R.id.frame_layout,fragmentE, "map")
                tb.visibility = View.GONE
            }
        }
        transaction.addToBackStack(null)
        transaction.setTransition(FragmentTransaction.TRANSIT_FRAGMENT_FADE)
        transaction.commit()
        return true
    }
````
`onNavigationItemSelected`를 재정의하여 사용한다.  
바텀 내비게이션 뷰에서 각각의 아이템을 선택할 때, 조건을 걸어준다.  
트랜잭션을 이용하여 다음과 같이 구현한다.  

replace, commit을 마치게되면 정상적으로 프레그먼트의 이동이 출력된다.

<br>

## 결론

<iframe width="1280" height="753" src="https://www.youtube.com/embed/qq1VPB03nWc" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

<br>

위의 영상에서 보듯 바텀 내비게이션 뷰의 아이템 선택에 따라 각기 다른 프래그먼트를 출력함을 알 수 있다.

<br>

## 향후과제

- 스크롤 할 때, 상하단바 숨기는 방법  
- 뒤로가기 버튼을 눌렀을 때, 바텀 내비게이션 뷰의 아이템도 적용시키기

<br>

## 참고자료

<https://recipes4dev.tistory.com/58>  
<https://lktprogrammer.tistory.com/182>

<br>

*Writer: Jae-Hwan Lee*