---
toc: true
toc_sticky: true
categories:
  - Android
title: Android - 여러 개의 입력을 받는 Custom Dialog 만들기 (1) (Kotlin)
tags: [Kotlin, 코틀린, JAVA, Android, Dialog]
excerpt: "안드로이드에서 여러 개의 입력을 받는 Custom Dialog 만들어보기"
---

* **연구주제** :Android - 여러 개의 입력을 받는 Custom Dialog 만들기 (Kotlin)
* **연구목적** : 안드로이드에서의 코틀린 활용
* **연구일시** : 2020년 03월 27일 09:00~17:00
* **연구자** : 이재환 <ljh951103@naver.com>
* **연구장비** : HP EliteDesk 800 G4 TWR, Kotlin, Android studio, IntelliJ
* **관련연구** : Java, Android, Kotlin, Dialog

<br>

## 서론

이번 시간에는 여러개의 입력을 받는 커스텀 다이얼로그를 만들어보자.  
다음의 사진이 결과물이다.

![image](https://user-images.githubusercontent.com/57826388/76698023-610ae480-66e1-11ea-967e-e52889ba6585.png)

![image](https://user-images.githubusercontent.com/57826388/76698025-65370200-66e1-11ea-95b8-8a7a7f34a699.png)

<br>

위와 같이 1개에서 5개까지 추가로 입력받을 수 있는 다이얼로그를 구현해 볼 것이다.

<br>

## 본론

### **레이아웃**

레이아웃은 기본적으로 `TableLayout`을 사용하고 추가로 사용자가 필요한 UI가 있는 경우, 다른 레이아웃으로 감싸서 사용할 수 있다.

````xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:layout_margin="50dp"
    android:orientation="vertical"
    xmlns:android="http://schemas.android.com/apk/res/android">

<TableLayout
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:layout_gravity="center_horizontal"
    android:paddingTop="20dp"
    xmlns:android="http://schemas.android.com/apk/res/android">

    <TableRow
        android:id="@+id/tag1"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:layout_marginBottom="5dp"
        android:gravity="center_horizontal"
        android:orientation="horizontal">

        <ImageView
            android:id="@+id/tag1_add"
            android:layout_width="40dp"
            android:layout_height="40dp"
            android:src="@drawable/ic_add" />
        <TextView
            android:gravity="center"
            android:textSize="18dp"
            android:layout_marginLeft="5dp"
            android:layout_height="match_parent"
            android:text="태그 1 : " />

        <EditText
            android:id="@+id/tag1_edit"
            android:layout_width="150dp"
            android:layout_height="match_parent"
            android:layout_marginRight="5dp"
            android:hint="Input tag"
            android:inputType="text" />

        <ImageView
            android:id="@+id/tag1_remove"
            android:layout_width="40dp"
            android:layout_height="40dp"
            android:src="@drawable/ic_remove"
            android:visibility="invisible"/>
    </TableRow>

    <TableRow
        android:id="@+id/tag2"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:gravity="center_horizontal"
        android:layout_marginBottom="5dp"
        android:orientation="horizontal"
        android:visibility="gone">

        <ImageView
            android:id="@+id/tag2_add"
            android:layout_width="40dp"
            android:layout_height="40dp"
            android:src="@drawable/ic_add" />
        <TextView
            android:gravity="center"
            android:textSize="18dp"
            android:layout_marginLeft="5dp"
            android:layout_height="match_parent"
            android:text="태그 2 : " />

        <EditText
            android:id="@+id/tag2_edit"
            android:layout_width="150dp"
            android:layout_height="match_parent"
            android:layout_marginRight="5dp"
            android:hint="Input tag"
            android:inputType="text" />

        <ImageView
            android:id="@+id/tag2_remove"
            android:layout_width="40dp"
            android:layout_height="40dp"
            android:src="@drawable/ic_remove" />
    </TableRow>

    <TableRow
        android:id="@+id/tag3"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:layout_marginBottom="5dp"
        android:gravity="center_horizontal"
        android:orientation="horizontal"
        android:visibility="gone">

        <ImageView
            android:id="@+id/tag3_add"
            android:layout_width="40dp"
            android:layout_height="40dp"
            android:src="@drawable/ic_add" />
        <TextView
            android:gravity="center"
            android:textSize="18dp"
            android:layout_marginLeft="5dp"
            android:layout_height="match_parent"
            android:text="태그 3 : " />

        <EditText
            android:id="@+id/tag3_edit"
            android:layout_width="150dp"
            android:layout_height="match_parent"
            android:layout_marginRight="5dp"
            android:hint="Input tag"
            android:inputType="text" />

        <ImageView
            android:id="@+id/tag3_remove"
            android:layout_width="40dp"
            android:layout_height="40dp"
            android:src="@drawable/ic_remove" />
    </TableRow>

    <TableRow
        android:id="@+id/tag4"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:layout_marginBottom="5dp"
        android:gravity="center_horizontal"
        android:orientation="horizontal"
        android:visibility="gone">

        <ImageView
            android:id="@+id/tag4_add"
            android:layout_width="40dp"
            android:layout_height="40dp"
            android:src="@drawable/ic_add" />
        <TextView
            android:gravity="center"
            android:textSize="18dp"
            android:layout_marginLeft="5dp"
            android:layout_height="match_parent"
            android:text="태그 4 : " />

        <EditText
            android:id="@+id/tag4_edit"
            android:layout_width="150dp"
            android:layout_height="match_parent"
            android:layout_marginRight="5dp"
            android:hint="Input tag"
            android:inputType="text" />

        <ImageView
            android:id="@+id/tag4_remove"
            android:layout_width="40dp"
            android:layout_height="40dp"
            android:src="@drawable/ic_remove" />
    </TableRow>

    <TableRow
        android:id="@+id/tag5"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:gravity="center_horizontal"
        android:layout_marginBottom="5dp"
        android:orientation="horizontal"
        android:visibility="gone">

        <ImageView
            android:id="@+id/tag5_add"
            android:layout_width="40dp"
            android:layout_height="40dp" />
        <TextView
            android:gravity="center"
            android:textSize="18dp"
            android:layout_marginLeft="5dp"
            android:layout_height="match_parent"
            android:text="태그 5 : " />

        <EditText
            android:id="@+id/tag5_edit"
            android:layout_width="150dp"
            android:layout_height="match_parent"
            android:layout_marginRight="5dp"
            android:hint="Input tag"
            android:inputType="text" />

        <ImageView
            android:id="@+id/tag5_remove"
            android:layout_width="40dp"
            android:layout_height="40dp"
            android:src="@drawable/ic_remove" />
    </TableRow>
</TableLayout>


    <LinearLayout
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:orientation="horizontal"
        android:layout_gravity="center_horizontal"
        xmlns:android="http://schemas.android.com/apk/res/android">

        <Button
            android:id="@+id/tag_cancel"
            android:layout_width="107dp"
            android:layout_height="wrap_content"
            android:text="취소"
            android:layout_marginRight="15dp"
            android:textSize="20dp"
            android:textAllCaps="false"
            android:gravity="center_horizontal"
            android:layout_marginBottom="20dp"/>


        <Button
            android:id="@+id/tag_save"
            android:layout_width="107dp"
            android:layout_height="wrap_content"
            android:layout_marginLeft="15dp"
            android:text="저장"
            android:textSize="20dp"
            android:textAllCaps="false"
            android:gravity="center_horizontal"
            android:layout_marginBottom="20dp"/>

    </LinearLayout>
</LinearLayout>
````

<br>

이 레이아웃 코드의 경우에는 처음 호출했을 떄, 필요한 부분만 보여지고 나머지 부분은 다음과 같이 감춰져있다.  
`+, -` 버튼을 누를 때 마다 해당 뷰가 나타나고, 다른 뷰가 사라지는 동작을 하기위해 이렇게 코드를 작성한다.

![image](https://user-images.githubusercontent.com/57826388/76698155-f22e8b00-66e2-11ea-8d53-4ede6dda3e8e.png)

<br>

본 레이아웃의 계층구초는 다음과 같다.

![image](https://user-images.githubusercontent.com/57826388/76698216-8a2c7480-66e3-11ea-9a61-5f91de1d2314.png)
![image](https://user-images.githubusercontent.com/57826388/76698238-db3c6880-66e3-11ea-8a15-d614c12532dc.png)

<br>

## 결론

커스텀 다이얼로그의 레이아웃을 작성해보았다.  
바로 다음 포스팅에서는 다이얼로그를 액티비티에 연결하는 것을 다루겠다.

<br>

## 향후과제

커스텀 다이얼로그 클래스 작성

<br>

## 참고자료

<br>

*Writer: Jae-Hwan Lee*

