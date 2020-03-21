---
toc: true
toc_sticky: true
categories:
  - Android
title: Android - Firebase ML Kit을 활용한 이미지 라벨 지정 기기별 API 활용 (1)(Kotlin)
tags: [Kotlin, 코틀린, JAVA, Android, Firebase, API]
excerpt_separator: <!--more-->
---

# Android - Firebase ML Kit을 활용한 이미지 라벨 지정 기기별 API 활용 (1) (Kotlin)
<!--more-->
* **연구주제** : Android - Firebase 이미지 라벨 지정 기기별 API 활용 (Kotlin)
* **연구목적** : 안드로이드에서의 코틀린 활용
* **연구일시** : 2020년 04월 01일 09:00~17:00
* **연구자** : 이재환 <ljh951103@naver.com>
* **연구장비** : HP EliteDesk 800 G4 TWR, Kotlin, Android studio, IntelliJ
* **관련연구** : Java, Android, Kotlin, Firebase, API

<br>

## 서론

이미지 분석 API를 활용해야하는 경우가 많다.  
내가 진행중인 프로젝트의 경우 사진 관리 앱으로써, 사용자마다 로컬에 있는 이미지를 전부 API를 적용시켜야 하는 경우이다. 

그러나 `KAKAO, GOOGLE, AWS, MS` 등 많은 IT 기업들은 이 API를 클라우드로 제공하고 있고 무료 할당량이 많아야 월 5000개 정도이다.  
내 폰에만 수만장의 사진이 있기 때문에 이를 이용하여 서비스를 상용화 하기에는 무리가 있다.

그래서 찾은 것이 `Firebase ML Kit`이다.  
Firebase ML Kit의 이미지 라벨 지정 기기별 API를 이용하면 클라우드 서버와의 통신없이, 사용 제한 없이 이용할 수 있다.

<br>

![image](https://user-images.githubusercontent.com/57826388/76699068-f495e280-66ec-11ea-8510-53e8609802f4.png)

![image](https://user-images.githubusercontent.com/57826388/76699039-9963f000-66ec-11ea-89ed-203c4b0ea0b5.png)

이 API를 적용하기 이전에 우선 Firebase를 설치해야한다.  
이번 포스팅에서는 파이어베이스 설치 과정을 다루겠다.

<br>

## 본론

시작하기에 앞서서 공식 홈페이지에도 설명이 상세히 되어있으니 참고하길 바란다.

https://firebase.google.com/docs/ml-kit/android/label-images

<br>

### **Firebase - App 연결**

우선 파이어베이스에 로그인하여 앱과 연결해야한다.  
그러기 위해선 우선 프로젝트부터 만든다.

<br>

#### **1. 프로젝트 생성**

https://console.firebase.google.com/

여기로 접속하여 프로젝트 추가를 클릭한 후 프로젝트 이름을 입력한다.  
그 후, 프로젝트 만들기를 클릭한다.

<br>

#### **2. 앱 등록**

이후, 애플리케이션 ID를 입력한다.  
모듈(앱 수준) Gradle 파일(app/build.gradle)에서 이 애플리케이션 ID(com.yourcompany.yourproject)를 확인한다.

기타 정보를 입력한 뒤, 앱 등록을 클릭한다.

<br>

#### **3. 구성 파일 추가**

내 프로젝트에 구성파일을 추가해야한다. 

`Download google-services.json`

이것을 클릭하여 구성파일을 다운로드한다.  
그리고 앱의 루트 디렉토리에 이 json 파일을 넣어준다.

그 후, 앱에서 사용할 수 있도록 프로젝트 수준, gradle 파일에 다음과 같이 추가한다.

````kotlin
buildscript {

  repositories {
    // Check that you have the following line (if not, add it):
    google()  // Google's Maven repository
  }

  dependencies {
    // ...

    // Add the following line:
    classpath 'com.google.gms:google-services:4.2.0'  // Google Services plugin
  }
}

allprojects {
  // ...

  repositories {
    // Check that you have the following line (if not, add it):
    google()  // Google's Maven repository
    // ...
  }
}
````

<br>

마찬가지로 앱 수준, Gradle 파일에도 추가한다.

````kotlin
apply plugin: 'com.android.application'

android {
  // ...
}

// Add the following line to the bottom of the file:
apply plugin: 'com.google.gms.google-services'  // Google Play services Gradle plugin
````

<br>

#### **4. Firebase SDK 추가**

마지막으로 Firebase SDK를 추가한다. 앱 수준, Gradle 파일에 다음과 같이 추가한다.

````kotlin
dependencies {
 // ...
 implementation 'com.google.firebase:firebase-core:17.0.0'
}
````

이제 sync를 하게되면 파이어베이스가 정상적으로 설치되고 앱과 연결되었음을 확인할 수 있을 것이다.

<br>

## 결론

이번 포스팅에선 파이어베이스 설치만을 다루었고 다음 포스팅에서는 ML Kit를 활용하는 것에 대해 다루도록 하겠다.

<br>

## 향후과제

Firebase ML Kit를 활용

<br>

## 참고자료

<https://firebase.google.com/docs/android/setup>

<br>

*Writer: Jae-Hwan Lee*

