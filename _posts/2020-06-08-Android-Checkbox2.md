---
toc: true
toc_sticky: true
categories:
  - Android
title: Android - RecyclerView & Checkbox 활용 - 전체 체크박스 숨기기/보이기 (2) (Kotlin)
tags: [Kotlin, 코틀린, JAVA, Android, Checkbox]
excerpt: "리사이클러뷰에 연결된 뷰 객체 체크박스 전체를 이벤트에 따라 숨김/보임 처리를 해보겠습니다."
---

# 체크박스 숨김/보임 처리

## 개요

아래의 사진처럼 상단바의 휴지통 버튼을 누르면 리사이클러뷰에 숨겨져 있던 각각의 체크박스들이 모두 보이게 되는 부분을 다루겠습니다.

![image](https://user-images.githubusercontent.com/57826388/80909472-afdd0e00-8d63-11ea-9d51-c6e31fcb39f2.png)

![image](https://user-images.githubusercontent.com/57826388/80909473-b23f6800-8d63-11ea-901f-1ce440704e4c.png)

<br>

## 레이아웃

저번 포스팅에서 소개한 레이아웃에 다음을 추가합니다.

```xml
android:visibility="gone"
```

````xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.coordinatorlayout.widget.CoordinatorLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:orientation="vertical">
    <ImageView
        android:id="@+id/thumbnail_img"
        android:layout_width="100dp"
        android:layout_height="100dp"
        android:scaleType="centerCrop"
        android:background="@color/bar" />
    <CheckBox
        android:id="@+id/checkbox"
        android:layout_margin="3dp"
        android:layout_width="30dp"
        android:layout_height="30dp"
        android:button="@android:color/transparent"
        android:background="@drawable/checkbox"
        android:checked="false"
        android:visibility="gone"/>

</androidx.coordinatorlayout.widget.CoordinatorLayout>
````

<br>

## 어댑터에 분기 제어값 추가

어댑터에 다음 변수를 추가해줍니다.  
그리고 이 변수의 값에 따라 뷰가 바인딩 될 때, 체크박스의 숨김/보임을 성정하도록 합니다.

<br>


> 어댑터
> 
```kotlin
private var ck = 0
```

```kotlin
inner class Holder(itemView: View?) : RecyclerView.ViewHolder(itemView!!) {
        var thumbnail: ImageView = itemView!!.findViewById<ImageView>(R.id.thumbnail_img)
        var checkbox: CheckBox = itemView!!.findViewById<CheckBox>(R.id.checkbox)

        fun bind(data : thumbnailData, num: Int) {
            val layoutParam = thumbnail.layoutParams as ViewGroup.MarginLayoutParams
            thumbnail.layoutParams.width = size
            thumbnail.layoutParams.height = size
            layoutParam.setMargins(padding_size, padding_size, padding_size, padding_size)

            if(ck == 1) {
                checkbox.visibility = View.VISIBLE
            }
            else
                checkbox.visibility = View.GONE

            thumbnail.setImageResource(0)
            // 이미지 입력 값 작성 코드 필요
        }
    }
```

<br>

## 제어 함수 설정

그럼 외부에서 이벤트에 따라서 해당 어댑터의 `ck` 값을 변경시켜줘야합니다.  

다음의 함수를 생성하고 외부에서 조작하도록 구현합니다.

``` kotlin
fun updateCheckbox(n: Int) {
        ck = n
    }
```

<br>

> 액티비티

`appbar2.setExpanded(true,true)`는 상단 툴바를 활성화 시키는 코드입니다.  

n값에 따라 좀전에 만든 함수를 실행시켜서 변수를 제어합니다.
변수를 바꾼 후, `notifyDataSetChanged()`를 통해 뷰를 업데이트 시켜줍니다.

```kotlin
private fun btck(n: Int) {
        appbar2.visibility = View.VISIBLE
        appbar2.setExpanded(true,true)
        recyclerAdapter.updateCheckbox(n)
        recyclerAdapter.notifyDataSetChanged()
        
        if(n==1) {
            photolist_delete.visibility = View.GONE
            photolist_deleteok.visibility = View.VISIBLE
            photolist_deletecancel.visibility = View.VISIBLE
            radiobt.visibility = View.VISIBLE
        }
        else {
            photolist_delete.visibility = View.VISIBLE
            photolist_deleteok.visibility = View.GONE
            photolist_deletecancel.visibility = View.GONE
            radiobt.visibility = View.GONE
        }
    }
```

<br>

이제 각각의 이벤트에 다음의 함수를 호출하도록 합니다.  

btch(1)이면 체크박스를 보이도록 하고, btch(0)이면 체크박스를 숨기록 합니다.

```kotlin
photolist_delete.setOnClickListener {
            btck(1)
        }
        photolist_deletecancel.setOnClickListener {
            btck(0)
        }
```

<br>

## 결과

이제 지정한 이벤트를 주어서 동작시켜보면 정상적으로 바인딩 되는 체크박스가 포함된 객체가 업데이트 되는 것을 알 수 있습니다.

<br>

## 향후계획

다음과 같이 구현하면 잘 동작합니다. 하지만 체크를 하고 스크롤 시, 체크박스가 해제되는 문제점이 있습니다. 이것에 대해 다음 포스팅에서 다루어 보도록 하겠습니다.
