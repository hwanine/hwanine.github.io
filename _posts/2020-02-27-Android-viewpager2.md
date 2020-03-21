---
toc: true
toc_sticky: true
categories:
  - Android
title: Android - 뷰 페이저와 상 하단바 같이 사용하기 (Kotlin)
tags: [Kotlin, 코틀린, JAVA, Android, ViewPager]
excerpt: "안드로이드 뷰 페이저와 상 하단바 같이 사용하기"
---

* **연구주제** : Android - 뷰 페이저와 상 하단바 같이 사용하기 (Kotlin)
* **연구목적** : 안드로이드에서의 코틀린 활용
* **연구일시** : 2020년 02월 27일 09:00~17:00
* **연구자** : 이재환 <ljh951103@naver.com>
* **연구장비** : HP EliteDesk 800 G4 TWR, Kotlin, Android studio, IntelliJ
* **관련연구** : Java, Android, Kotlin, ViewPager

<br>
   
## 서론

이번 시간에는 뷰 페이저와 상, 하단바 같이 사용하는 것에 대해 다루겠다.  
사실 뷰 페이저와 상, 하단바를 같이 사용하는 것은 이전에 상, 하단바와 프래그먼트를 구성했던 것과 크게 다르지않다.  
하지만 이번에는 상, 하단바를 같이 사용하고 메인 뷰 페이저에서 어댑터 뷰로의 접근까지 다루도록 하겠다.

<br>
   
## 본론

목표는 뷰 페이저와는 별도로 상, 하단바를 고정시키고 페이지 스와이프에 따라 상, 하단바의 값이 변하도록 한다.  
그리고 다른 뷰의 접근을 위해 어댑터로의 접근을 하여 각각 뷰의 터치로 상, 하단바를 숨길 수 있도록 한다.  

우선 레이아웃은 다음과 같다.

<br>

### **레이아웃**

#### **메인 뷰페이저**

````xml
<?xml version="1.0" encoding="utf-8"?>
<?xml version="1.0" encoding="utf-8"?>
<androidx.coordinatorlayout.widget.CoordinatorLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    tools:context=".PhotoViewPager"
    android:background="#000000">

    <com.ortiz.touchview.TouchImageView
        android:id="@+id/sub_img"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:layout_gravity="center_vertical"
        android:transitionName="pair_thumb"/>

    <androidx.viewpager.widget.ViewPager

        android:id="@+id/imgViewPager"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        tools:ignore="MissingConstraints">



    </androidx.viewpager.widget.ViewPager>

    <com.google.android.material.appbar.AppBarLayout
        android:id="@+id/appbar3"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:background="#30000000"
        app:elevation="0dp">

        <androidx.appcompat.widget.Toolbar
            android:id="@+id/mainphoto_toolbar"
            android:layout_width="match_parent"
            android:layout_height="@dimen/top_info"
            android:minHeight="@dimen/top_info"
            android:theme="@style/AppTheme"

            android:visibility="invisible"
            app:titleMargin="0dp">

            <LinearLayout
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:orientation="vertical">

                <androidx.appcompat.widget.AppCompatTextView
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="이름: "
                    android:textColor="#FFFFFF" />

                <androidx.appcompat.widget.AppCompatTextView
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="날짜: "
                    android:textColor="#FFFFFF" />

                <androidx.appcompat.widget.AppCompatTextView
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="장소: "
                    android:textColor="#FFFFFF" />

                <androidx.appcompat.widget.AppCompatTextView
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="태그: "
                    android:textColor="#FFFFFF" />
            </LinearLayout>

            <LinearLayout
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:orientation="vertical">

                <androidx.appcompat.widget.AppCompatTextView
                    android:id="@+id/imgView_text"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:textColor="#FFFFFF" />

                <androidx.appcompat.widget.AppCompatTextView
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="2020. 02. 25"
                    android:textColor="#FFFFFF" />

                <androidx.appcompat.widget.AppCompatTextView
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="창원시 의창구 창원대학로 20"
                    android:textColor="#FFFFFF" />

                <androidx.appcompat.widget.AppCompatTextView
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="코로나, 창원, 여권"
                    android:textColor="#FFFFFF" />
            </LinearLayout>


        </androidx.appcompat.widget.Toolbar>
    </com.google.android.material.appbar.AppBarLayout>

    <!-- 하단바 -->
    <com.google.android.material.bottomnavigation.BottomNavigationView
        android:id="@+id/bottom_photo_menu"
        android:layout_width="match_parent"
        android:layout_height="@dimen/bottom_size"
        android:layout_gravity="bottom"
        android:layout_marginStart="0dp"
        android:layout_marginEnd="0dp"
        android:background="#30000000"
        android:minHeight="@dimen/bottom_size"
        android:visibility="invisible"
        app:elevation="0dp"
        app:itemIconTint="#FFFFFF"
        app:itemTextColor="#FFFFFF"
        app:labelVisibilityMode="labeled"
        app:layout_behavior="com.google.android.material.behavior.HideBottomViewOnScrollBehavior"
        app:menu="@menu/menu_photo_option" />

</androidx.coordinatorlayout.widget.CoordinatorLayout>
````

<br>

#### **내부 뷰페이저**

````xml
<androidx.coordinatorlayout.widget.CoordinatorLayout xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">

    <com.ortiz.touchview.TouchImageView
        android:id="@+id/imgView"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:layout_gravity="center_vertical"
        android:src="@drawable/loding_image"
        android:transitionName="pair_thumb"/>

</androidx.coordinatorlayout.widget.CoordinatorLayout>
````

<br>

### **상, 하단바 업데이트**

아이템 뷰를 클릭했을 때, 인텐트로 넘어온 값으로 우선 상, 하단바의 초기값으로 지정하고 페이지를 이동하게 한다.

````
name.setText(photoList[position].data)
viewPager.setCurrentItem(position, false)
````

<br>

그리고 페이지가 변한 때 마다 상, 하단바의 뷰를 변경시킨다.

````kotlin
viewPager.addOnPageChangeListener(object : ViewPager.OnPageChangeListener {

            override fun onPageScrollStateChanged(state: Int) { }
            override fun onPageScrolled(position: Int, positionOffset: Float, positionOffsetPixels: Int) {
                if(check == false ) {
                    viewPager.setCurrentItem(index, false)
                    check = true
                }
                subimg!!.setImageResource(0)    // 애니메이션
                tb.visibility = View.VISIBLE
                bt.visibility = View.VISIBLE
            }
            override fun onPageSelected(position: Int) {
                check_index = position
                text_name.setText(photoList[position].data)
            }
        })
````

<br>

### **상, 하단바 숨기기**

현재 뷰페이저 액티비티에 상, 하단바가 있지만 사진을 클릭했을 때 인식하지 않는다.  
왜냐하면 메인 뷰페이저 레이아웃을 사용하고 있기 때문이다.  
메인 뷰페이저는 프레임을 제공하는것이지 실제 레이아웃은 내부 뷰페이저가 덮어씌우고 있는 모양이기 때문이다.  
그래서 우리는 내부 뷰페이저의 이미지 객체에 클릭리스너를 달아서 처리해야한다.  
그것을 어댑터에서 처리하는데 메인 뷰페이저의 뷰를 이용하기위한 방법은 다음과 같다,

- layoutinflater를 이용하여 뷰 객체 생성하여 접근
- 메소드를 타고 접근

메소드를 타고 접근하여 뷰를 처리해보자.

````kotlin
recyclerAdapter =
            PagerRecyclerAdapter(
                this,
                thumbnailList, toolbar, bottombar
            )
````

<br>

우리는 툴바와 하단바 두개의 뷰를 사용할 것 이다.  
다음과 같이 어댑터에 파라미터를 두개 추가하여 호출하게 한다..  
물론 어댑터의 파라미터에도 추가한다.

````kotlin
public class PagerRecyclerAdapter(private val context: Context, var list: List<thumbnailData>, var tb: View, var bt: View) : PagerAdapter() {
    private var layoutInflater: LayoutInflater? = null
    private var check: Boolean = false

    override fun isViewFromObject(view: View, `object`: Any): Boolean {
        return view === `object`
    }

    override fun getCount(): Int {
        return list.size
    }

    override fun instantiateItem(container: ViewGroup, position: Int): Any {

        layoutInflater = context.getSystemService(Context.LAYOUT_INFLATER_SERVICE) as LayoutInflater
        val v = layoutInflater!!.inflate(R.layout.photoview_pager, null)
        val v2 = layoutInflater!!.inflate(R.layout.photoview_frame, null)
        val image = v.findViewById<View>(R.id.imgView) as ImageView
        val vp = container as ViewPager
        vp.addView(v, 0)
        image.setOnClickListener(object : View.OnClickListener {
            override fun onClick(v: View?) {
                if(check == false) {
                   // Log.d("이건?", tb.toString())
                    tb.visibility = View.GONE
                    bt.visibility = View.GONE
                    check = true
                }
                else {
                    tb.visibility = View.VISIBLE
                    bt.visibility = View.VISIBLE
                    check = false
                }
            }
        })
        return v
    }

    override fun destroyItem(container: ViewGroup, position: Int, `object`: Any) {
        val vp = container as ViewPager
        val v = `object` as View
        vp.removeView(v)
    }

    fun setThumbnailList(list : List<thumbnailData>) {
        this.list = list
        notifyDataSetChanged()
    }
}
````

<br>
   
## 결론

<iframe width="871" height="490" src="https://www.youtube.com/embed/pTAq3NPZQlw" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

<br>

위의 영상을 보면 코드가 정상적으로 동작하여 상, 하단바와 뷰 페이저를 출력하고있음을 확인할 수 있다. 

## 향후과제

화면 전환 애니메이션 및 더블클릭 방지

<br>

## 참고자료

<br>

*Writer: Jae-Hwan Lee*

