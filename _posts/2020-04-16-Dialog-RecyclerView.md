---
toc: true
toc_sticky: true
categories:
  - Android
title: Android - 커스텀 다이얼로그에 리사이클러뷰 구성하기 (1) (Kotlin)
tags: [Kotlin, 코틀린, JAVA, Android, Dialog, RecyclerView]
excerpt: "커스텀 다이얼로그에 이미지들로 구성된 리사이클러뷰를 구성해보자."
---

* **연구주제** : Android - 커스텀 다이얼로그에 리사이클러뷰 구성하기 (1) (Kotlin)
* **연구목적** : 안드로이드에서의 코틀린 활용
* **연구일시** : 2020년 04월 16일 09:00~17:00
* **연구자** : 이재환 <ljh951103@naver.com>
* **연구장비** : HP EliteDesk 800 G4 TWR, Kotlin, Android studio, IntelliJ
* **관련연구** : Java, Android, Kotlin, Dialog, RecyclerView

<br>
   
## 서론

예전 포스팅에서 리사이클러 뷰를 구현해보았고, 또한 커스텀 다이얼로그도 몇번 만들어 보았습니다.  
이번에는 커스텀 다이얼로그에 이미지 리사이클러 뷰를 구현해 보겠습니다.  
구현하고자 하는 것은 유사 이미지들을 검색하여 이를 보존하고 나머지 이미지들을 지우는 로직입니다. 
여기서 우리는 화면을 구성하는 것만 다루어 보겠습니다.  

완성 화면은 다음과 같습니다.

처음 버튼을 클릭하면 유자 썸네일 목록을 불러와서 보여주는 다이얼로그를 출력합니다.

![image](https://user-images.githubusercontent.com/57826388/77818863-7a685380-7119-11ea-9b31-6d3b175ebda8.png)

<br>

특정 이미지를 클릭하면 이미지를 불러오며 다음과 같은 다이얼로그를 출력합니다.

![image](https://user-images.githubusercontent.com/57826388/77818868-7f2d0780-7119-11ea-9cda-86cb6548d425.png)

<br>

원하는 이미지를 추가하였으면 다음과 같은 저장 다이얼로그를 출력하게 됩니다.

![image](https://user-images.githubusercontent.com/57826388/77818872-82c08e80-7119-11ea-9628-23f25705da4e.png)

<br>
   
## 본론

### **레이아웃**

우선 만들고자 하는 화면의 레이아웃을 구성해줍니다.  
각자 원하는 데이터를 넣으셔서 작성하시면 됩니다.  
RecyclerView에 이미지가 들어가게 됩니다. 

````xml
<LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">

    <LinearLayout
        android:id="@+id/linearLayout7"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="vertical"
        android:layout_marginBottom="5dp"
        app:layout_constraintBottom_toBottomOf="parent">

        <LinearLayout
            android:id="@+id/linearLayoudt7"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:orientation="horizontal">
            <ImageView
                android:id="@+id/title_typee"
                android:src="@drawable/ic_similar_photo"
                android:layout_width="30dp"
                android:layout_height="30dp"
                android:layout_marginTop="3dp"
                android:layout_marginLeft="3dp"
                android:layout_marginRight="5dp"/>
        <TextView
            android:id="@+id/similar_carncel"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="유사 이미지 목록"
            android:textStyle="bold"
            android:textAllCaps="false"
            android:textSize="26dp" />
        </LinearLayout>

        <TextView
            android:id="@+id/wer"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_marginLeft="10dp"
            android:text="가장 괜찮은 사진을 선택해주세요."
            android:textAllCaps="false"
            android:textSize="18dp" />
    </LinearLayout>


    <FrameLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        tools:ignore="MissingConstraints"
        android:layout_weight="10">

        <androidx.recyclerview.widget.RecyclerView
            android:id="@+id/similar_RecycleView"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:scrollbars="vertical" />
    </FrameLayout>

    <LinearLayout
        android:id="@+id/linearLayout"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:gravity="center_horizontal"
        android:orientation="horizontal"
        app:layout_constraintBottom_toBottomOf="parent">

        <Button
            android:id="@+id/similar_cancel"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_marginRight="15dp"
            android:gravity="center_horizontal"
            android:text="취소"
            android:textAllCaps="false"
            android:textSize="20dp" />


        <Button
            android:id="@+id/similar_ok"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_marginLeft="15dp"
            android:gravity="center_horizontal"
            android:text="저장"
            android:textAllCaps="false"
            android:textSize="20dp" />

    </LinearLayout>
</LinearLayout>
````

코드를 작성하시면 안드로이스 스튜디오에 다음과 같이 화면이 보일겁니다.

![image](https://user-images.githubusercontent.com/57826388/77819194-48a4bc00-711c-11ea-96a1-fa104022229e.png)

<br>

### **다이얼로그 클래스**

그리고 다이얼로그를 생성하는 클래스를 작성하고 메소드를 재정의합니다.  
여기서 리사이클러뷰에 데이터를 삽입하는 작업은 각자의 데이터를 이용하여 목적에 맞게 하시면 됩니다.

````kotlin
class similarImageDialog(v: View): DialogFragment() {

    private lateinit var recyclerAdapter : RecyclerAdapterPhoto
    private lateinit var recyclerView : RecyclerView
    private val v = v

    @RequiresApi(Build.VERSION_CODES.N)

    override fun onCreateDialog(savedInstanceState: Bundle?): Dialog {
        recyclerView = v.findViewById<RecyclerView>(R.id.similar_RecycleView)

        val maindlgBuilder: androidx.appcompat.app.AlertDialog.Builder = androidx.appcompat.app.AlertDialog.Builder(    // 메인 다이얼로그
            context!!, android.R.style.Theme_DeviceDefault_Light_Dialog_NoActionBar_MinWidth)
        maindlgBuilder.setView(v)

        val dlg = maindlgBuilder.create()
        saveSimilarPhoto(dlg)
        return dlg
    }
}
````

<br>

### **호출**

이제 자신이 만든 다이얼로그를 호출하여 생성하는 코드를 작성합니다.  
저는 이를 함수로 구성하였습니다.

````kotlin
private fun similarImage() {
        val similarImageDialogView: View = layoutInflater.inflate(R.layout.similar_image_layout, null)
        val dlg = similarImageDialog(similarImageDialogView)
        dlg.setCancelable(false)
        dlg.show(supportFragmentManager, "similarImageDialog")
    }
````

<br>

이후 이를 등록할 리스너에서 호출해줍니다.

````kotlin
similarImage()
````

<br>

## 결론

리사이클러 뷰를 담고있는 커스텀 다이얼로그를 만들어 보았습니다.  
다음 포스팅에서는 서론의 결과 사진들 처럼 클릭이벤트에 따라 또다른 커스텀 다이얼로그를 생성해 보겠습니다.

<br>

## 향후과제

클릭 이벤트 및 추가 커스텀 다이얼로그 호출

<br>

## 참고자료

<br>

*Writer: Jae-Hwan Lee*

