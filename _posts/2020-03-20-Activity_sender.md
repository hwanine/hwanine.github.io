---
toc: true
toc_sticky: true
categories:
  - Android
title: Android - Activity에서 Intent를 통해 데이터를 전달하고 받아오기 (Kotlin)
tags: [Kotlin, 코틀린, JAVA, Android, Activity, Intent]
excerpt: "안드로이드 Activity에서 Intent를 통해 데이터를 전달하고 받아오기"
---

* **연구주제** : Android - Activity에서 Intent를 통해 데이터를 전달하고 받아오기 (Kotlin)
* **연구목적** : 안드로이드에서의 코틀린 활용
* **연구일시** : 2020년 03월 20일 09:00~17:00
* **연구자** : 이재환 <ljh951103@naver.com>
* **연구장비** : HP EliteDesk 800 G4 TWR, Kotlin, Android studio, IntelliJ
* **관련연구** : Java, Android, Kotlin, Activity, Intent

<br>

## 서론

안드로이드에서 액티비티를 이동할 때, `startActivity()`을 이용하여 이동할 수 있다.  
이번에는 기본적인 화면이동말고 데이터를 전달하고, 다시 데이터를 얻어오는 로직에 대해 다루어본다.

<br>

## 본론

### **화면 이동 및 데이터 전달**

다음과같이 intent 객체에 putExtra를 통해 값을 전달한다.  
첫 번째 인자는 받은 값을 식별할 이름이고 두 번째 인자는 전달할 데이터이다.  
그리고 `startActivityForResult(intent, 100)` 를 통해 화면 이동과 동시에 코드 100을 저장한다.  
이 코드는 다시 결과데이터를 받을 때, 식별하기위해 사용한다.
````kotlin
val intent = Intent(this, PhotoViewPager::class.java)
                intent.putExtra("photo_num", num)
                intent.putExtra("thumbnail", PhotoData.photo_id)
                startActivityForResult(intent, 100)
````

<br>

### **이동된 액티비티에서 데이터 전달받기**

다음과 같이 getExtra를 통해 식별 이름을 입력하여 데이터를 전달받을 수 있다.  
두 번쨰 인자는 초기 default 값이다.

````kotlin
 fun getExtra(){
        if (intent.hasExtra("photo_num") && intent.hasExtra("photo_list")) {
            thumbnail = intent.getLongExtra("thumbnail", 0)
            index = intent.getIntExtra("photo_num", 0)
        }
        else {
            Toast.makeText(this, "전달된 이름이 없습니다", Toast.LENGTH_SHORT).show()
        }
    }
````

<br>

### **이동된 액티비티에서 다시 데이터 전달하기**

이번 예제에서는 백버튼을 통해 구현했다.  
이동된 액티비티에서 다시 데이터를 전달하려면 입력 데이터를 똑같이 putExtra로 준다.  
그리고 결과 코드와 함께 `setResult()` 를 호출하고 종료한다.
````kotlin
    override fun onBackPressed() {
        val intent = Intent()
        intent.putExtra("index", index)
        setResult(Activity.RESULT_OK, intent)
        finish()
    }
````

<br>

### **원래 액티비티에서 데이터 전달받기**

resultCode는 이전에 `setResult()`에서 입력했던 코드가 된다.  
requestCode는 처음 액티비티를 이동할 떄, 입력했던 코드다.

이 두 조건에 해당하게되면 바로 이 구간에서 다음과 같이 데이터를 전달받는다.

````kotlin
    override fun onActivityResult(requestCode: Int, resultCode: Int, data: Intent?) {
        super.onActivityResult(requestCode, resultCode, data)
        if (resultCode == Activity.RESULT_OK) {
            when (requestCode) {
                100 -> {
                    val doc = data!!.getIntExtra("index", 0)
                }
            }
        }
    }
````

## 결론

이렇듯 액티비티로 화면전환만 가능한게 아니라 데이터를 전송할 수도 있고, 마찬가지로 데이터를 전달받아서 동작을 처리할 수도 있다.

<br>

## 향후과제

<br>

## 참고자료

<https://lktprogrammer.tistory.com/151>

<br>

*Writer: Jae-Hwan Lee*
