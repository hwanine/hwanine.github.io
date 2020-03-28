---
toc: true
toc_sticky: true
categories:
  - Android
title: Android - 커스텀 다이얼로그 클릭 이벤트 및 추가 다이얼로그 생성 (2) (Kotlin)
tags: [Kotlin, 코틀린, JAVA, Android, Dialog, RecyclerView]
excerpt: "커스텀 다이얼로그 객체들의 클릭이벤트에 따라 또다른 커스텀 다이얼로그를 생성해 보겠습니다."
---

* **연구주제** : Android - 커스텀 다이얼로그에 리사이클러뷰 구성하기 (2) (Kotlin)
* **연구목적** : 안드로이드에서의 코틀린 활용
* **연구일시** : 2020년 04월 17일 09:00~17:00
* **연구자** : 이재환 <ljh951103@naver.com>
* **연구장비** : HP EliteDesk 800 G4 TWR, Kotlin, Android studio, IntelliJ
* **관련연구** : Java, Android, Kotlin, Dialog, RecyclerView

<br>
   
## 서론

이번 포스팅에서는 여러 클릭 이벤트에 따라 다이얼로그를 호출하는 부분을 다루어 보도록 하겠습니다.  
저번 시간에 구현한 화면은 다음과 같습니다.

![image](https://user-images.githubusercontent.com/57826388/77818863-7a685380-7119-11ea-9b31-6d3b175ebda8.png)

<br>

여기서 특정 이미지 클릭에 따라 다음과 같은 다이얼로그를 생성하고

![image](https://user-images.githubusercontent.com/57826388/77818868-7f2d0780-7119-11ea-9cda-86cb6548d425.png)

<br>

저장 버튼에 따라 다음의 다이얼로그를 생성하기 까지의 작업입니다.

![image](https://user-images.githubusercontent.com/57826388/77818872-82c08e80-7119-11ea-9628-23f25705da4e.png)

<br>
   
## 본론

### **레이아웃**

우선 이미지를 클릭하였을 때의 생성되는 다이얼로그 레이아웃을 작성해봅니다.

````xml
<LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">

    <androidx.coordinatorlayout.widget.CoordinatorLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:layout_weight="10">

        <com.ortiz.touchview.TouchImageView
            android:id="@+id/select_photo"
            android:layout_width="360dp"
            android:layout_height="640dp"
            android:layout_gravity="center_vertical|center_horizontal"/>

    </androidx.coordinatorlayout.widget.CoordinatorLayout>

    <LinearLayout
        android:id="@+id/linearLayout"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:gravity="center_horizontal"
        android:orientation="horizontal"
        app:layout_constraintBottom_toBottomOf="parent">

        <Button
            android:id="@+id/select_cancel"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_marginRight="15dp"
            android:gravity="center_horizontal"
            android:text="취소"
            android:textAllCaps="false"
            android:textSize="20dp" />


        <Button
            android:id="@+id/select_ok"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_marginLeft="15dp"
            android:gravity="center_horizontal"
            android:text="확인"
            android:textAllCaps="false"
            android:textSize="20dp" />

    </LinearLayout>
</LinearLayout>
````

모두 작성해보시면 다음과 같은 화면이 보일겁니다.  
제목 부분이 보이지 않는 것은 우리는 이 부분을 커스텀으로 생성하지않고 `Builder`로 생성할 것이기 때문입니다.

![image](https://user-images.githubusercontent.com/57826388/77819228-81dd2c00-711c-11ea-80fc-80397477bd5a.png)

<br>

### **추가 다이얼로그**

이제 클래스를 작성해 줍니다.  
여기서는 특정 이미지 클릭에 따른 이벤트로 그 이미지를 불러와 화면을 구성합니다.  
제가 작성하고 있는 프로그램의 것들이 포함되어있기 때문에 주석을 참조해 주시면 되겠습니다.  
확인 및 취소 클릭 리스너도 함께 달아줍니다.

````kotlin
private fun setView(list: ArrayList<thumbnailData>) {
        recyclerAdapter =
            RecyclerAdapterPhoto(activity, list) { thumbnailData, num ->
            // 여기서부터
                val similarImageSelectView: View = layoutInflater.inflate(R.layout.similar_image_select, null)
                ImageLoder.execute(ImageLoad(context!!, similarImageSelectView.select_photo, thumbnailData.photo_id))
                val dlgBuilder: androidx.appcompat.app.AlertDialog.Builder = androidx.appcompat.app.AlertDialog.Builder(    // 확인 다이얼로그
                    context!!,  android.R.style.Theme_DeviceDefault_Light_Dialog_NoActionBar_MinWidth)
                dlgBuilder.setCancelable(false)

                dlgBuilder.setView(similarImageSelectView)
                dlgBuilder.setTitle("보존할 사진이 맞나요?")
                dlgBuilder.setIcon(R.drawable.ic_image)
                val dlgselect = dlgBuilder.create()
                dlgselect.show()
                similarImageSelectView.select_cancel.setOnClickListener{
                    dlgselect.cancel()
                }
                similarImageSelectView.select_ok.setOnClickListener{
                    similarList.removeAt(num)
                    dlgselect.cancel()
                    Toast.makeText(context!!, "입력 완료 되었습니다. \n저장을 누르시면 입력된 사진들만 저장됩니다.", Toast.LENGTH_SHORT).show()
                }

            }
            // 여기까지
        recyclerView.adapter = recyclerAdapter
        val lm = GridLayoutManager(context, 2)
        recyclerView.layoutManager = lm
    }
````

<br>

### **확인 다이얼로그**

다음은 저장 버튼을 눌렀을 때, 생성되는 다이얼로그 입니다.  
여기서도 각자의 프로그램에 맞게 작성해주시면 됩니다.

````kotlin
private fun saveSimilarPhoto(dlg: androidx.appcompat.app.AlertDialog) {
        v.similar_cancel.setOnClickListener {
            dlg.cancel()
        }
        v.similar_ok.setOnClickListener {
            val warningBuilder: androidx.appcompat.app.AlertDialog.Builder = androidx.appcompat.app.AlertDialog.Builder(context!!,    // 경고 다이얼로그
                android.R.style.Theme_DeviceDefault_Light_Dialog_NoActionBar_MinWidth)
            warningBuilder.setTitle("알림") //제목
            warningBuilder.setMessage("선택한 사진들을 제외하고는 모든 사진이 삭제됩니다.\n정말 저장하시겠습니까?\n\n (선택한사진: ${selectnum} 개)") // 메시지
            warningBuilder.setCancelable(false)
            warningBuilder.setPositiveButton("확인", DialogInterface.OnClickListener { dialog, which ->
                dialog.cancel()
                dlg.cancel()
                val pager: Activity = context!! as Activity        // 액티비티 종료
                pager.finish()
                Toast.makeText(context!!, "${selectnum} 개의 사진이 저장 완료 되었습니다. \n", Toast.LENGTH_SHORT).show()
            })
            warningBuilder.setNegativeButton("취소", DialogInterface.OnClickListener { dialog, which ->
                dialog.cancel()
            })
            val dlgWarning = warningBuilder.create()
            dlgWarning.show()
        }

    }
````

<br>

### **호출**

마지막으로 작성한 다이얼로그들을 `onCreateDialog`에 호출하여 합쳐줍니다.

````kotlin
override fun onCreateDialog(savedInstanceState: Bundle?): Dialog {
        setView(ArrayList())

        val maindlgBuilder: androidx.appcompat.app.AlertDialog.Builder = androidx.appcompat.app.AlertDialog.Builder(    // 메인 다이얼로그
            context!!, android.R.style.Theme_DeviceDefault_Light_Dialog_NoActionBar_MinWidth)
        maindlgBuilder.setView(v)

        val dlg = maindlgBuilder.create()
        saveSimilarPhoto(dlg)
        return dlg
    }
````

<br>

## 결론

리사이클러 뷰를 담고있는 커스텀 다이얼로그를 만들고 모든 추가적인 다이얼로그를 생성해서 화면을 구성해보았습니다.  
다음 포스팅에서는 내부 로직을 포함하고 소스코드 공개와 리뷰를 해보겠습니다.

<br>

## 향후과제

유사 이미지 검색 내부 로직 작성 및 리뷰

<br>

## 참고자료

<br>

*Writer: Jae-Hwan Lee*
