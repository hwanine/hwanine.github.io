---
layout: post
title: Android - Kakaolink를 활용하여 카톡으로 공유하기(V2) (Kotlin)
feature-img: "assets/img/pexels/computer.jpeg"
tags: [Kotlin, 코틀린, JAVA, Android, Share, Kakaolink]
author: Jae-Hwan Lee
excerpt_separator: <!--more-->
---

# Android - Kakaolink를 활용하여 카톡으로 공유하기(V2) (Kotlin)
<!--more-->
* **연구주제** : Android - Kakaolink를 활용하여 카톡으로 공유하기(V2) (Kotlin)
* **연구목적** : 안드로이드에서의 코틀린 활용
* **연구일시** : 2020년 03월 26일 09:00~17:00
* **연구자** : 이재환 <ljh951103@naver.com>
* **연구장비** : HP EliteDesk 800 G4 TWR, Kotlin, Android studio, IntelliJ
* **관련연구** : Java, Android, Kotlin, RecyclerView, Share, Kakaolink]

<br>

## 서론

앱에서 카카오톡으로 이동하여 카카오 링크를 전송할 수 있다. 

![image](https://user-images.githubusercontent.com/57826388/76158452-39ec6a00-6159-11ea-9d62-e6a623adcd06.png)

이런 링크 많이 보았을 것이다. 이것이 카카오 링크를 활용한 것이다  
V1 기준으로 작성된 예제들이 많던데 이제 V1 카카오링크는 활용할 수 없으므로 이번 글에서는 V2를 기준으로 다루겠다.  
이제 카카오 링크 API를 활용하여 앱 링크를 전송해보자.

<br>

## 본론

### **개발자 계정 설정**

우선 카카오톡 개발자 페이지에서 앱을 생성해야한다.

[카카오 개발자 페이지](https://developers.kakao.com/apps)

다음 페이지에서 앱을 만들고

![image](https://user-images.githubusercontent.com/57826388/76158514-d7479e00-6159-11ea-94e2-94e7840067c4.png)

<br>

`일반` 탭으로 이동하여 플랫폼 패키지에 대한 정보를 입력해준다.

![image](https://user-images.githubusercontent.com/57826388/76158535-1aa20c80-615a-11ea-9064-873b88419fb9.png)

<br>

### **그래들 설정**

우선 앱 그래들에서 다음과 같이 링크 sdk를 추가한다.

````
//KAKAO SDK
implementation group: 'com.kakao.sdk', name: 'kakaolink', version: project.KAKAO_SDK_VERSION
````

<br>

그리고 프로퍼티의 마지막에 다음을 추가한다.

````
KAKAO_SDK_GROUP=com.kakao.sdk
KAKAO_SDK_VERSION=1.27.0
````

<br>

마지막으로 프로젝트 그래들에서도 sdk를 사용하기위해 다음과 같이 명시해준다.

````
subprojects {
    repositories {
        mavenCentral()
        maven { url 'http://devrepo.kakao.com:8088/nexus/content/groups/public/' }
    }
}
````

<br>

### **키 파일 생성**

/res/values에 `kakao_string.xml` 파일을 생성하여 다음의 내용을 입력한다.  
key는 조금 전에 개발자 페이지에서 확인했던 네이티브 키를 입력한다.

````
<resources>
    <string name="kakao_app_key">AAAAAAAAAAAAAAAAAAAAAA</string>
    <string name="kakao_scheme">kakaoAAAAAAAAAAAAAAAAAAAAAA</string>
    <string name="kakaolink_host">kakaolink</string>
</resources>
````

<br>

### **매니페스트 추가**

다음과 같이 권한을 추가하고

````
<uses-permission android:name="android.permission.INTERNET"/>
````

<br>

사용하는 액티비티 안에 앱을 호출하기위한 스키마를 추가한다.
````
...

<activity
    android:name=".PhotoViewPager"
    android:theme="@style/PhotoTheme">
    <intent-filter>
        <action android:name="android.intent.action.VIEW"/>
        <category android:name="android.intent.category.DEFAULT"/>
        <category android:name="android.intent.category.BROWSABLE"/

        <data 
            android:scheme="@string/kakao_scheme"
            android:host="@string/kakaolink_host"/>
    </intent-filter>
</activity>
<meta-data
    android:name="com.kakao.sdk.AppKey"
    android:value="@string/kakao_app_key" />

...
````

<br>

### **템플릿 추가**

특정 버튼과 연결하여 공유할 템플릿 소스를 다음의 예제로 입력해준다.

````
fun kakaoLink() {
        val params = FeedTemplate
            .newBuilder(
                ContentObject.newBuilder(
                        "디저트 사진",
                        "http://mud-kage.kakao.co.kr/dn/NTmhS/btqfEUdFAUf/FjKzkZsnoeE4o19klTOVI1/openlink_640x640s.jpg",
                        LinkObject.newBuilder().setWebUrl("https://developers.kakao.com")
                            .setMobileWebUrl("https://developers.kakao.com").build()
                    )
                    .setDescrption("아메리카노, 빵, 케익")
                    .build()
            )
            .setSocial(
                SocialObject.newBuilder().setLikeCount(10).setCommentCount(20)
                    .setSharedCount(30).setViewCount(40).build()
            )
            .addButton(
                ButtonObject(
                    "웹에서 보기",
                    LinkObject.newBuilder().setWebUrl("https://developers.kakao.com").setMobileWebUrl(
                        "https://developers.kakao.com"
                    ).build()
                )
            )
            .addButton(
                ButtonObject(
                    "앱에서 보기", LinkObject.newBuilder()
                        .setWebUrl("'https://developers.kakao.com")
                        .setMobileWebUrl("https://developers.kakao.com")
                        .setAndroidExecutionParams("key1=value1")
                        .setIosExecutionParams("key1=value1")
                        .build()
                )
            )
            .build()

        val serverCallbackArgs: MutableMap<String, String> =
            HashMap()
        serverCallbackArgs["user_id"] = "\${current_user_id}"
        serverCallbackArgs["product_id"] = "\${shared_product_id}"

        KakaoLinkService.getInstance().sendDefault(
            this,
            params,
            serverCallbackArgs,
            object : ResponseCallback<KakaoLinkResponse?>() {
                override fun onFailure(errorResult: ErrorResult) {
                    //Logger.e(errorResult.toString())
                }

                override fun onSuccess(result: KakaoLinkResponse?) { // 템플릿 밸리데이션과 쿼터 체크가 성공적으로 끝남. 톡에서 정상적으로 보내졌는지 보장은 할 수 없다. 전송 성공 유무는 서버콜백 기능을 이용하여야 한다.
                }
            })
    }
````

<br>

## 결론

이제 버튼을 활성화 하여 위의 예제 메소드를 호출하면 링크가 제대로 전송되어 공유가 될 것이다!

<br>

## 향후과제

<br>

## 참고자료

<https://thisisspear.tistory.com/52?category=823575>  
<https://developers.kakao.com/docs/android/kakaotalk-link>

<br>

*Writer: Jae-Hwan Lee*