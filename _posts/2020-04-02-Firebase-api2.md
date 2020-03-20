---
layout: post
title: Android - Firebase 이미지 라벨 지정 기기별 API 활용 (2)(Kotlin)
thumbnail: "https://user-images.githubusercontent.com/57826388/76699068-f495e280-66ec-11ea-8510-53e8609802f4.png"
feature-img: "assets/img/pexels/computer.jpeg"
tags: [Kotlin, 코틀린, JAVA, Android, Firebase, API]
author: Jae-Hwan Lee
excerpt_separator: <!--more-->
---

# Android - Firebase ML Kit을 활용한 이미지 라벨 지정 기기별 API 활용 (2) (Kotlin)
<!--more-->
* **연구주제** : Android - Firebase ML Kit을 활용한 이미지 라벨 지정 기기별 API 활용 (Kotlin)
* **연구목적** : 안드로이드에서의 코틀린 활용
* **연구일시** : 2020년 04월 02일 09:00~17:00
* **연구자** : 이재환 <ljh951103@naver.com>
* **연구장비** : HP EliteDesk 800 G4 TWR, Kotlin, Android studio, IntelliJ
* **관련연구** : Java, Android, Kotlin, Firebase, API

<br>

## 서론

저번 포스팅에서 Firebase에 앱을 등록하고 프로젝트와 연결하여 설치하는 부분까지 다루었다.  

이번에는 본격적으로 API를 적용하여 활용하는 부분을 다루어보도록 하겠다.

<br>

## 본론

### **ML Kit API 추가**

#### **1. 라이브러리 추가**

앱 수준, Gradle 파일에 ML Kit를 다음과 같이 등록해준다.

````
dependencies {
  // ...

  implementation 'com.google.firebase:firebase-ml-vision:24.0.0'
  implementation 'com.google.firebase:firebase-ml-vision-image-label-model:19.0.0'
}
apply plugin: 'com.google.gms.google-services'
````

<br>

추가로 기기에 머신러닝 모델을 자동으로 다운로드 하도록 앱을 설정한다.

````
<application ...>
  ...
  <meta-data
      android:name="com.google.firebase.ml.vision.DEPENDENCIES"
      android:value="label" />
  <!-- To use multiple models: android:value="label,model2,model3" -->
</application>
````

<br>

#### **2. 이미지 레이블러 구성 및 실행**

먼저 `FirebaseVisionImageLabeler`의 인스턴스를 가져온다.

````
val labeler = FirebaseVision.getInstance().getOnDeviceImageLabeler()
````

<br>

그 후, 이미지를 processImage() 메소드에 전달한다.

````
labeler.processImage(image)
    .addOnSuccessListener { labels ->
      // Task completed successfully
      // ...
    }
    .addOnFailureListener { e ->
      // Task failed with an exception
      // ...
    }
````

<br>

#### **3. 이미지 객체정보 가져오기**

다음과 같이 전달한 이미지의 객체 정보를 출력할 수 있다.

````
for (label in labels) {
  val text = label.text
  val entityId = label.entityId
  val confidence = label.confidence
}
````

<br>

### **ML Kit API 사용**

![image](https://user-images.githubusercontent.com/57826388/76699823-73424e00-66f4-11ea-8ebe-d5d2d2f7fd0f.png)

비트맵 이미지를 넣고 실행하면 다음과 같이 태그와 신뢰도가 올바르게 출력된다.

<br>

## 결론

이번 포스팅까지 파이어베이스 설치부터 ML Kit를 활용하는 것에 대해 다루었다.  

그러나 위의 결과사진을 보면 태그 값이 영어로 표현된다.  
다음 포스팅에서는 여기서 출력된 태그값들을 번역Api를 활용하여 한글로 번역하여 출력하는 부분을 다루겠다.

<br>

## 향후과제

Firebase ML Kit를 활용

<br>

## 참고자료

<https://firebase.google.com/docs/android/setup>  
<https://firebase.google.com/docs/ml-kit/android/label-images>

<br>

*Writer: Jae-Hwan Lee*

