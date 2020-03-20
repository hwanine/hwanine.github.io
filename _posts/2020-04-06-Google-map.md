---
layout: post
title: Android - Google Map API 설치부터 심화 활용까지 (설치) (1) (Kotlin)
feature-img: "assets/img/pexels/computer.jpeg"
tags: [Kotlin, 코틀린, JAVA, Android, Google, API]
author: Jae-Hwan Lee
excerpt_separator: <!--more-->
---

# Android - Google Map 설치부터 심화 활용까지 (설치) (1) (Kotlin)
<!--more-->
* **연구주제** : Android - Google Map 활용 (Kotlin)
* **연구목적** : 안드로이드에서의 코틀린 활용
* **연구일시** : 2020년 04월 06일 09:00~17:00
* **연구자** : 이재환 <ljh951103@naver.com>
* **연구장비** : HP EliteDesk 800 G4 TWR, Kotlin, Android studio, IntelliJ
* **관련연구** : Java, Android, Kotlin, Google, API

<br>

## 서론

![image](https://user-images.githubusercontent.com/57826388/76702881-bd392d00-6710-11ea-8219-cf8c0665adc7.png)

일상 속에서 맵은 굉장히 활용도가 높다.  
개발자 입장에서 앱을 개발할 때, 맵을 활용하고자 한다면 당연 구글 맵이 1순위가 된다.  
왜냐하면 구글 맵은 안드로이드 스마트폰의 기본 내장 애플리케이션이기 때문에 별 다른 제약사항이 없기 떄문이다.

이번 포스팅부터는 구글 맵 API 설치 뿐만 아니라 심화 활용까지 다루어 보겠다.

![image](https://user-images.githubusercontent.com/57826388/76700954-fc12b700-66ff-11ea-9c0f-8bf6dda16ea5.png)

![image](https://user-images.githubusercontent.com/57826388/76700957-00d76b00-6700-11ea-9029-0daa4bf04055.png)

최종 목표는 위의 사진의 지도처럼 만드는 것이다.

<br>

## 본론

우선 이번 포스팅에서 다룰 것은 API를 받아오는 것이다.

### **API Key 받아오기**

<ttps://console.cloud.google.com/apis>

다음 주소로가서 새 프로젝트를 생성해야 한다.

![image](https://user-images.githubusercontent.com/57826388/76701060-ece03900-6700-11ea-9ce7-accf78626a98.png)

위의 '새 프로젝트' 를 클릭한다.

<br>

![image](https://user-images.githubusercontent.com/57826388/76701091-48aac200-6701-11ea-881a-961942f98d88.png)

그리고 프로젝트 이름을 입력하고 '만들기' 를 클릭한다.

<br>

![image](https://user-images.githubusercontent.com/57826388/76701117-81e33200-6701-11ea-888f-71c9e94faea4.png)

프로젝트가 만들어졌으면 자신의 프로젝트로 간다.

<br>

![image](https://user-images.githubusercontent.com/57826388/76701135-b656ee00-6701-11ea-8d22-9139c63731e2.png)

좌측의 API 및 서비스 - 라이브러리로 이동한다.

<br>

![image](https://user-images.githubusercontent.com/57826388/76701142-bf47bf80-6701-11ea-8bc0-fb0e9bbabdee.png)

Maps SDK for Android 항목을 클릭한다.  
그리고 사용설정을 한다.

<br>

![image](https://user-images.githubusercontent.com/57826388/76701196-501e9b00-6702-11ea-84b8-5ed178d60848.png)

이제 본인의 페이지로 들어가 사용자 인증 정보를 확인하면 구글 맵 키를 확인할 수 있다.

<br>

### **안드로이드 스튜디오 설정**

````
<meta-data
    android:name="com.google.android.geo.API_KEY"
    android:value="@string/google_maps_key" />
````

위에서 확인한 키를 안드로이드 매니페스트 파일에 정의해준다.

<br>

![image](https://user-images.githubusercontent.com/57826388/76701280-316cd400-6703-11ea-8078-5de3a35ee1cb.png)

다음과 같이 Tools > SDK Manager > SDK Tools 에서 Google Play services를 체크하고 설치를 진행한다.

<br>

이제 앱 수준, Gradle 파일에 구글 플레이 라이브러리를 추가한 후, async를 눌러준다.

````
dependencies {
    implementation fileTree(dir: 'libs', include: ['*.jar'])
    implementation 'androidx.appcompat:appcompat:1.0.2'
    implementation 'androidx.constraintlayout:constraintlayout:1.1.3'
    implementation 'com.google.android.gms:play-services-maps:17.0.0'
    implementation 'com.google.android.gms:play-services-location:17.0.0'
    testImplementation 'junit:junit:4.12'
    androidTestImplementation 'androidx.test:runner:1.2.0'
    androidTestImplementation 'androidx.test.espresso:espresso-core:3.2.0'
}
````

<br>

### **구글 맵 띄우기**

이번 예시에서는 프래그먼트로 띄워보겠다.

<br>

````
class MapFragment : Fragment(),  OnMapReadyCallback {

    private lateinit var mMap: GoogleMap
    override fun onCreateView(

        inflater: LayoutInflater, container: ViewGroup?,
        savedInstanceState: Bundle?
    ): View? {
        val rootView: View = inflater.inflate(R.layout.fragment_map, container, false)
        val mapFragment = childFragmentManager.findFragmentById(R.id.mapview) as SupportMapFragment

        mapFragment.getMapAsync(this)

        // Inflate the layout for this fragment

        return rootView
    }

    override fun onMapReady(googleMap: GoogleMap) {
        mMap = googleMap
        val marker = LatLng(35.241615, 128.695587)
        mMap.addMarker(MarkerOptions().position(marker).title("Marker LAB"))
        mMap.moveCamera(CameraUpdateFactory.newLatLng(marker))
    }
}
````

<br>

````
?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools" android:orientation="vertical" android:layout_width="match_parent"
    android:layout_height="match_parent">

    <fragment
        android:id="@+id/mapview"
        android:name="com.google.android.gms.maps.SupportMapFragment"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        tools:layout="@layout/abc_search_view" />

</LinearLayout>
````

<br>

코드를 실행시켜보면 맵이 정상적으로 실행됨을 알 수 있다.

<br>

## 결론

구글 맵 API를 받아와서 실행 시키는 것 까지 해보았다.  다음 포스팅부터는 맵을 활용하는 부분에 대해 다루겠다. 

<br>

## 향후과제

구글 맵 활용

<br>

## 참고자료

<https://webnautes.tistory.com/647>

<br>

*Writer: Jae-Hwan Lee*
