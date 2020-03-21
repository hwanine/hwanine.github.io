---
toc: true
toc_sticky: true
categories:
  - Android
title: Android - 위험 권한, 권한 전용 팝업 생성 (Kotlin)
tags: [Kotlin, 코틀린, JAVA, Android, Dialog, Permission]
excerpt: "안드로이드에서 위험 권한, 권한 전용 팝업 생성"
---

* **연구주제** : Android - 위험 권한, 권한 전용 팝업 생성 (Kotlin)
* **연구목적** : 안드로이드에서의 코틀린 활용
* **연구일시** : 2020년 03월 12일 09:00~17:00
* **연구자** : 이재환 <ljh951103@naver.com>
* **연구장비** : HP EliteDesk 800 G4 TWR, Kotlin, Android studio, IntelliJ
* **관련연구** : Java, Android, Kotlin, Dialog, Permission

<br>

## 서론

안드로이드에서 앱을 사용할 떄, SDK가 마시멜로버전(APK 23)이상일 경우 특정 기능을 사용할 떄 권한을 요청한다.  
이 권한 중, 위험 권한으로 분류된 권한은 개발자가 사용자에게 직접 물을 수 있도록 작성해야한다.

페이지에서 확인한 위험 권한 리스트들을 매니페스트에 작성하게 된다면, 매니페스트 작성 + 코드로 작성 까지 두 번을 작업해야한다는 의미이다.

이제 안드로이드10이 출시되는 시점이라 이 권한 작성 코드는 필수적이라고 생각한다.

<br>

## 본론  

### **위험 권한, 권한 전용 팝업**

````kotlin
 private fun checkPermission() {
        if (ContextCompat.checkSelfPermission(this, Manifest.permission.WRITE_EXTERNAL_STORAGE) !== PackageManager.PERMISSION_GRANTED)
        {
            
            if (ActivityCompat.shouldShowRequestPermissionRationale(this, Manifest.permission.WRITE_EXTERNAL_STORAGE))
            {
                AlertDialog.Builder(this)
                    .setTitle("알림")
                    .setMessage("저장소 권한이 거부되었습니다. 사용을 원하시면 설정에서 해당 권한을 직접 허용하셔야 합니다.")
                    .setNeutralButton("설정", object: DialogInterface.OnClickListener {
                        override fun onClick(dialogInterface:DialogInterface, i:Int) {
                            val intent = Intent(Settings.ACTION_APPLICATION_DETAILS_SETTINGS)
                            intent.setData(Uri.parse("package:" + getPackageName()))
                            startActivity(intent)
                        }
                    })
                    .setPositiveButton("확인", object:DialogInterface.OnClickListener {
                        override fun onClick(dialogInterface:DialogInterface, i:Int) {
                            finish()
                        }
                    })
                    .setCancelable(false)
                    .create()
                    .show()
            }
            else
            {
                ActivityCompat.requestPermissions(this, arrayOf<String>(Manifest.permission.WRITE_EXTERNAL_STORAGE, Manifest.permission.READ_EXTERNAL_STORAGE), MY_PERMISSION_STORAGE)
            }
        }
    }
````

````kotlin
override fun onRequestPermissionsResult(requestCode:Int, @NonNull permissions:Array<String>, @NonNull grantResults:IntArray) {
        when (requestCode) {
            MY_PERMISSION_STORAGE -> for (i in grantResults.indices)
            {
                // grantResults[] : 허용된 권한은 0, 거부한 권한은 -1
                if (grantResults[i] < 0)
                {
                    Toast.makeText(this@MainActivity, "해당 권한을 활성화 하셔야 합니다.", Toast.LENGTH_SHORT).show()
                    return
                }
            }
        }// 허용했다면 이 부분에서..
    }
````

````kotlin
    companion object {
        private val MY_PERMISSION_STORAGE = 1111
    }
````

<br>

## 결론

![image](https://user-images.githubusercontent.com/57826388/76139741-9e92c080-6096-11ea-9e84-bf9d73003af0.png)

처음 접속했을 떄, 다음과 같은 권한 허용 알림이 나오는데 이를 거부할 경우, 권한을 해야한다는 토스트메시지가 출력된다.

<br>

![image](https://user-images.githubusercontent.com/57826388/76139770-f8938600-6096-11ea-9b8e-4f797add468a.png)

그리고 앱을 종료시킨 후, 다시 실행시키면 다음과 같이 알림이 출력된다.  
설정을 클릭할 경우 앱 권한 설정화면으로 이동하게 된다.


<br>

![image](https://user-images.githubusercontent.com/57826388/76139855-c171a480-6097-11ea-92d6-51f2a76ea28b.png)

<br>

## 향후과제

<br>

## 참고자료

<https://g-y-e-o-m.tistory.com/47>  

<br>

*Writer: Jae-Hwan Lee*