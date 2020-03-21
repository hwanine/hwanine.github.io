---
toc: true
toc_sticky: true
categories:
  - Android
title: Android - Shared Element Transition 화면 전환 애니메이션 및 더블클릭 방지 (Kotlin)
tags: [Kotlin, 코틀린, JAVA, Android, Transition, Shared Element Transition, Animation]
excerpt_separator: <!--more-->
---

# Android - Shared Element Transition 화면 전환 애니메이션 및 더블클릭 방지 (Kotlin)
<!--more-->
* **연구주제** : Android - Shared Element Transition 화면 전환 애니메이션 및 더블클릭 방지 (Kotlin)
* **연구목적** : 안드로이드에서의 코틀린 활용
* **연구일시** : 2020년 02월 28일 09:00~17:00
* **연구자** : 이재환 <ljh951103@naver.com>
* **연구장비** : HP EliteDesk 800 G4 TWR, Kotlin, Android studio, IntelliJ
* **관련연구** : Java, Android, Kotlin, Transition, Shared Element Transition, Animation

<br>
   
## 서론

### **Shared Element Transition**

사용자 지정 애니메이션이라고 불린다.  
액티비티나 프래그먼트를 전환할 때, 동적인 움직임을 보여주는 방법이라고 할 수 있다.  
머테리얼 디자인을 기점으로 동작하기 때문에 API 21 이상의 기기에서만 사용할 수 있다.  
우리는 이것으로 화면 전환 애니메이션을 구성해볼 것이다.

<br>
   
## 본론

### **1. 애니메이션 셜정**

리소스 디렉토리에 transition 폴더를 생성한 후, Transition resource file으로 xml 파일을 생성한다.  
그리고 다음과 같이 입력한다.

````xml
<transitionSet xmlns:android="http://schemas.android.com/apk/res/android">
    <changeBounds/>
</transitionSet>
````    
    
* changeBounds - 대상 뷰의 레이아웃 경계에서 변경을 애니메이트합니다.
* changeClipBounds - 대상 뷰의 클리핑 경계에서 변경을 애니메이트합니다.
* changeTransform - 대상 뷰의 배율 및 회전 변경을 애니메이트합니다.
* changeImageTransform - 대상 이미지의 크기 및 배율 변경을 애니메이트합니다.


<br>

### **2. 스타일 셜정**

````xml
   <style name="PhotoTheme" parent="Theme.MaterialComponents.Light.NoActionBar">
        <!-- Customize your theme here. -->
        <item name="colorPrimary">@color/common_google_signin_btn_text_light_focused</item>
        <item name="colorPrimaryDark">@android:color/black</item>
        <item name="colorAccent">@android:color/black</item>
        <item name="windowActionBar">false</item>
        <item name="windowNoTitle">true</item>
            <item name="android:windowContentTransitions">true</item>
            // 액티비티 내의 뷰들이 Transition이 가능하도록 한다
            <item name="android:windowSharedElementEnterTransition">@transition/custom_trans</item>
            // 액티비티 이동 시 입장 애니메이션
            <item name="android:windowSharedElementExitTransition">@transition/custom_trans</item>
            // 액티비티 이동 시 퇴장 애니메이션
    </style>
````

다음과 같이 사용하는 앱 스타일에 3가지 아이템을 정의하여준다.  
minSdkVersion이 21미만인 경우에는 styles.xml(v21)도 만들어 준다.

<br>

### **3. 스타일 적용**

매니페스트 파일에 스타일을 적용시켜준다.

````xml
 <activity android:name=".PhotoViewPager"
            android:theme="@style/PhotoTheme">
        </activity>
````

<br>

### **4. UI 설정**

시작하는 뷰와 완료되는 뷰에 다음과 같이 설정을 해주어야 한다.  
`android:transitionName=""`에 서로 같은 호칭을 입력준다.

````xml
<com.ortiz.touchview.TouchImageView
        android:id="@+id/sub_img"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:layout_gravity="center_vertical"
        android:transitionName="pair_thumb"/>
````

````
<com.ortiz.touchview.TouchImageView
        android:id="@+id/imgView"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:layout_gravity="center_vertical"
        android:src="@drawable/loding_image"
        android:transitionName="pair_thumb"/>
````

<br>

### **5. 코드 적용**

````kotlin
if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.LOLLIPOP) {
                    val options = ActivityOptions.makeSceneTransitionAnimation(
                        this,
                        image, "pair_thumb"
                    )
                    startActivityForResult(intent, 100, options.toBundle())
                } else {
                    startActivityForResult(intent, 100)
            }
````

<br>

만약 움직이는 뷰가 여러개인 경우 다음과 같이 입력한다.

````kotlin
val options = ActivityOptions.makeSceneTransitionAnimation(MainActivity.this,
                            Pair.create((View)뷰1, "호칭1"),
                            Pair.create((View)뷰2, "호칭2"),
                            Pair.create((View)뷰3, "호칭3"));
````

<br>

### **6. 되돌리기**

돌아올 때에도 애니메이션이 적용되어야 한다. 따라서 `onBackPressed()`를 재정의 한다.

````kotlin
public void onBackPressed() {
        supportFinishAfterTransition();
    }
````

이제 실행시켜보면 애니메이션이 잘 동작되었음을 확인할 수 있다.  
하지만 몇번 써보다보면 버그를 마주하게 된다.  
아이템을 더블클릭해보아라. 버그가 생기지않는가?  
이 버그를 해결하기위해 더블클릭을 방지해보자.

<br>

### **7. 더블클릭 방지**

아이템 클릭 리스너에 if문으로 분기를 달아준다.

````kotlin
if(SystemClock.elapsedRealtime() - mLastClickTime > 1000) {
                정상적인 실행
            }
                mLastClickTime = SystemClock.elapsedRealtime()
````

`SystemClock.elapsedRealtime()`은 앱이 실행되고 지금까지의 시간을 나타낸다.  
mLastClickTime을 계속 주기적으로 SystemClock.elapsedRealtime()로 초기화하여 두 사이의 값차가 1000보다 작으면 더블클릭으로 간주하여 더블클릭을 방지하는 구조이다.

<br>
   
## 결론

<iframe width="871" height="490" src="https://www.youtube.com/embed/nfhIGlBQgho" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

<br>

위의 영상을 보면 애니메이션이 잘 동작하고있음을 확인할 수 있다.  
그러나 이동의 제약이나 오류 찾기의 어려움 같은 문제점 또한 존재한다.  
그럼에도 불구하고 애니메이션은 완성도 높은 앱을 만들기에는 꼭 필요한 기능중 하나라고 생각한다.

<br>

## 향후과제

<br>

## 참고자료

<https://ekutzblog.wordpress.com/2018/03/14/shared-element-transition/>  
<https://zerocool0713.tistory.com/3>

<br>

*Writer: Jae-Hwan Lee*
