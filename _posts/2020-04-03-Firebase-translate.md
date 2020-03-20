---
layout: post
title: Android - Firebase 텍스트 번역기 기기별 API 활용 (Kotlin)
thumbnail: "https://user-images.githubusercontent.com/57826388/76699068-f495e280-66ec-11ea-8510-53e8609802f4.png"
feature-img: "assets/img/pexels/computer.jpeg"
tags: [Kotlin, 코틀린, JAVA, Android, Firebase, API]
author: Jae-Hwan Lee
excerpt_separator: <!--more-->
---

# Android - Firebase ML Kit을 활용한 텍스트 번역기 기기별 API 활용 (Kotlin)
<!--more-->
* **연구주제** : Android - Firebase 텍스트 번역기 기기별 API 활용 (Kotlin)
* **연구목적** : 안드로이드에서의 코틀린 활용
* **연구일시** : 2020년 04월 03일 09:00~17:00
* **연구자** : 이재환 <ljh951103@naver.com>
* **연구장비** : HP EliteDesk 800 G4 TWR, Kotlin, Android studio, IntelliJ
* **관련연구** : Java, Android, Kotlin, Firebase, API

<br>

## 서론

이번 포스팅에서는 Firebase의 텍스트 번역 기기별 API에 대해 다루겠다.  
아래의 사진과 같이 이 API를 이용하면 모델을 기기에 다운로드하기 때문에 59개 언어간의 번역을 서버통신 없이 사용할 수 있다. 

![image](https://user-images.githubusercontent.com/57826388/76700395-4a24bc00-66fa-11ea-90ec-6f56a0218930.png)

<br>

## 본론

### **ML Kit API 추가**

#### **1. 라이브러리 추가**

앱 수준, Gradle 파일에 ML Kit를 다음과 같이 등록해준다.

````
dependencies {
  // ...

  implementation 'com.google.firebase:firebase-ml-natural-language:22.0.0'
  implementation 'com.google.firebase:firebase-ml-natural-language-translate-model:20.0.7'
}
apply plugin: 'com.google.gms.google-services'
````

<br>

#### **2. 출발어 도착어 구성**

다음과 같이 번역할 언어, 완료할 언어를 설정한다.

````
val options = FirebaseTranslatorOptions.Builder()
        .setSourceLanguage(FirebaseTranslateLanguage.EN)
        .setTargetLanguage(FirebaseTranslateLanguage.DE)
        .build()
val englishGermanTranslator = FirebaseNaturalLanguage.getInstance().getTranslator(options)
````

<br>

#### **2. 번역 모델 다운로드 확인**

번역 모델 다운로드를 확인한다. WIFI를 사용하는 경우에만 다운로드 한다.

````
englishGermanTranslator.downloadModelIfNeeded()
        .addOnSuccessListener {
            // Model downloaded successfully. Okay to start translating.
            // (Set a flag, unhide the translation UI, etc.)
        }
        .addOnFailureListener { exception ->
            // Model couldn’t be downloaded or other internal error.
            // ...
        }
````

<br>

#### **3. 번역 동작**

이제 번역기를 동작시킨다. 다운로드 된 것이 확인되면 텍스트 문자열을 전달하여 번역이 실행된다.

````
englishGermanTranslator.translate(text)
        .addOnSuccessListener { translatedText ->
            // Translation successful.
        }
        .addOnFailureListener { exception ->
             // Error.
             // ...
        }
````

<br>

#### ***초기에 명시적으로 다운로드***

사실 이러한 방식으로 코드를 작성하면 모델이 필요한 경우에 바로 번역이 안되는 경우가 있다.  
따라서 앱 초기에 명시적으로 다운로드 할 수도 있다.  

다음 코드는 로딩 액티비티에서 명시적으로 다운로드를 받게한다.

````
val modelManager = FirebaseModelManager.getInstance()
        val Model = FirebaseTranslateRemoteModel.Builder(FirebaseTranslateLanguage.KO).build()
        val conditions = FirebaseModelDownloadConditions.Builder()
            .requireWifi()
            .build()

        FirebaseModelManager.getInstance().isModelDownloaded(Model).addOnSuccessListener { isDownloaded ->
                val options = if (isDownloaded) {
                    startActivity(intent)
                    } else {
                        dlg.setTitle("환영합니다") //제목
                        dlg.setMessage("추가 파일 설치가 필요합니다. 와이파이를 연결해주세요. \n\n다운로드 하시겠습니까? (30mb)") // 메시지
                        dlg.setCancelable(false);
                        dlg.setPositiveButton("확인", DialogInterface.OnClickListener { dialog, which ->
                                loading()
                                modelManager.download(Model, conditions).addOnSuccessListener { modelManager.getDownloadedModels(
                                            FirebaseTranslateRemoteModel::class.java).addOnSuccessListener { models ->
                                                Toast.makeText(this@SplashActivity, "설치가 완료 되었습니다.", Toast.LENGTH_SHORT).show()
                                                startActivity(intent)
                                                finish()
                                                loadingEnd()
                                            }.addOnFailureListener {
                                    } 
                                }.addOnFailureListener {}
                        })
                        dlg.setNegativeButton("취소", DialogInterface.OnClickListener { dialog, which ->
                                System.exit(0)
                        })
                        dlg.show()
                    }
            }

    }
````

<br>

#### **번역 테스트**

이전에 했던 ML 이미지 라벨 지정 API에서 출력된 영어로 된 태그에 번역모델 API를 중첩 사용하여 출력해보았다.

````
val options = FirebaseTranslatorOptions.Builder()
                .setSourceLanguage(FirebaseTranslateLanguage.EN)
                .setTargetLanguage(FirebaseTranslateLanguage.KO)
                .build()
            val translator = FirebaseNaturalLanguage.getInstance().getTranslator(options)

            val path = MediaStore_Dao.getPathById(this, list[index].photo_id)
            var bitmap = BitmapFactory.decodeFile(path)
            bitmap =  MediaStore_Dao.modifyOrientaionById(this, list[index].photo_id, bitmap)

            val image = FirebaseVisionImage.fromBitmap(bitmap)
            val labeler = FirebaseVision.getInstance().getOnDeviceImageLabeler()
            labeler.processImage(image).addOnSuccessListener { labels ->
                    translator.downloadModelIfNeeded().addOnSuccessListener {
                            for (label in labels) {
                                translator.translate(label.text).addOnSuccessListener { translatedText ->
                                        if(label.confidence >= 0.7) {
                                            println("번호[" + index + "] " + "태그: " + translatedText)
                                            println("번호[" + index + "] " + "신뢰도: " + "${label.confidence}")
                                        }
                                    }.addOnFailureListener { exception ->
                                }
                            }
                        }.addOnFailureListener { exception ->
                    }
                }.addOnFailureListener { e ->
            }
````

![image](https://user-images.githubusercontent.com/57826388/76700696-84dc2380-66fd-11ea-9aae-1533e4a12164.png)

결과 값이 한글로 잘 출력됨을 알 수 있다.

<br>

## 결론

이번 포스팅까지 파이어베이스 설치부터 ML Kit의 이미지 라벨 지정 API, 텍스트번역 API를 활용하는 것에 대해 다루었다.  

이 API는 실제로 사용자의 데이터 통신을 요구하지 않으며 기기별 자체로 이미지를 처리하기 때문에 모바일 개발자 입장에서는 굉장히 소중한 API임이 틀림없는 것 같다.

<br>

## 향후과제

<br>

## 참고자료

<https://firebase.google.com/docs/android/setup>  
<https://firebase.google.com/docs/ml-kit/android/label-images>
<https://firebase.google.com/docs/ml-kit/android/translate-text>

<br>

*Writer: Jae-Hwan Lee*


