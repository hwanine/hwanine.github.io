---
toc: true
toc_sticky: true
categories:
  - Android
title: Android - RecyclerView & Checkbox 활용 - 이미지 객체에 체크박스 삽입 (1) (Kotlin)
tags: [Kotlin, 코틀린, JAVA, Android, Checkbox]
excerpt: "안드로이드에서 리사이클러뷰에 구현된 객체에 체크박스를 만들고, 이것을 제어하는 방법을 다루겠습니다."
---

# 리사이클러뷰 & 체크박스

## 개요

![image](https://user-images.githubusercontent.com/57826388/80822590-03d9dc80-8c16-11ea-8d0d-57c85f04a62a.png)

위의 사진은 리사이클러뷰에 이미지 뷰들을 객체로써 삽입하여 구현한 것 입니다.  

위의 사진과 같이 이번 포스팅부터 이후 포스팅까지 다음과 같은 내용을 다룹니다.

- 이미지 객체에 체크박스 삽입
- 전체 체크박스 숨기기/보이기
- 스크롤 시, 체크박스가 해제되는 바인딩 문제 해결
- RadioButton을 활용한 체크박스 전체 선택, 해제
- 체크박스 전체 삭제

리사이클러뷰에 대한 내용이나 구현하는 부분은 이미 구현되어있다고 가정하고 따로 다루지 않을 예정이니, 이전 포스팅을 참조해주세요. 

<https://hwanine.github.io/android/Android-RecyclerView/>

위의 링크를 참고해주세요.

<br>

## 이미지 객체에 체크박스 생성


> 레이아웃

레이아웃을 다음과 같이 작성하였습니다.  
체크박스에 대한 코드를 참조하여 사용하시면 됩니다.
체크박스는 체크 해제 상태로 삽입합니다.  

````xml
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
        android:checked="false"/>

</androidx.coordinatorlayout.widget.CoordinatorLayout>
````

위의 코드에서 

`android:background="@drawable/checkbox"`

이 부분은 별도의 커스텀 체크박스를 사용하였습니다. 커스텀 체크박스 생성에 관한 내용이 궁금하시다면 아래의 링크 포스팅을 참고해주세요.

<https://hwanine.github.io/android/Checkbox/>

<br>

## 어댑터와 뷰 객체 연결

이제 만든 이미지 객체를 어댑터와 연결해줍니다.

````kotlin
class RecyclerAdapterPhoto(val context: Activity?, var list: ArrayList<thumbnailData>) :
    RecyclerView.Adapter<RecyclerAdapterPhoto.Holder>()
{
    private lateinit var view: View

    inner class Holder(itemView: View?) : RecyclerView.ViewHolder(itemView!!) {
        var thumbnail: ImageView = itemView!!.findViewById<ImageView>(R.id.thumbnail_img)
        var checkbox: CheckBox = itemView!!.findViewById<CheckBox>(R.id.checkbox)

        fun bind(data : thumbnailData, num: Int) {
            val layoutParam = thumbnail.layoutParams as ViewGroup.MarginLayoutParams
            thumbnail.layoutParams.width = size
            thumbnail.layoutParams.height = size
            layoutParam.setMargins(padding_size, padding_size, padding_size, padding_size)


            thumbnail.setImageResource(0)
            // 이미지 생성하여 삽입하는 작업 필요

        }
    }

    override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): Holder {
        view = LayoutInflater.from(context).inflate(R.layout.thumbnail_imgview, parent, false)
        return Holder(view)
    }

    override fun getItemCount(): Int {
        return list.size
    }

    override fun onBindViewHolder(holder: Holder, position: Int) {
        holder.bind(list[position], position)
    }
}
````

<br>

## 액티비티와 어댑터 연결

액티비티와 어댑터를 연결해줍니다.  
각자의 뷰와 맞게 사용하시면 됩니다.

```kotlin
private lateinit var recyclerAdapter : RecyclerAdapterPhoto
private lateinit var recyclerView: RecyclerView
```

```kotlin
recyclerView = findViewById<RecyclerView>(R.id.photo_recyclerView)
recyclerAdapter =
            RecyclerAdapterPhoto(this,lst) 
        recyclerView.adapter = recyclerAdapter
        list = recyclerAdapter.getThumbnailList()
        val lm = GridLayoutManager(Main_PhotoView(), photo_type)
        recyclerView.layoutManager = lm
```

<br>

> 리사이클러뷰 레이아수

위의 어댑터, 뷰와 연동하여 구현된 레이아웃입니다. 참고용으로 사용하시거나 커스텀하여 사용하시면 됩니다.

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.coordinatorlayout.widget.CoordinatorLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">

    <!-- 프레임 -->
    <androidx.recyclerview.widget.RecyclerView
        android:id="@+id/photo_recyclerView"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        app:layout_behavior="@string/appbar_scrolling_view_behavior"/>


    <!-- 상단바 -->
    <com.google.android.material.appbar.AppBarLayout
        android:id="@+id/appbar2"
        android:layout_width="match_parent"
        android:layout_height="wrap_content">

        <androidx.appcompat.widget.Toolbar
            android:id="@+id/photo_toolbar"
            android:layout_width="match_parent"
            android:layout_height="@dimen/top_size"
            android:background="@color/bar"
            android:minHeight="@dimen/top_size"
            android:theme="@style/AppTheme"
            app:contentInsetStart="0dp"
            app:layout_scrollFlags="scroll|enterAlways">

            <LinearLayout
                android:layout_width="match_parent"
                android:layout_height="match_parent">
                <ImageView
                    android:id="@+id/title_type"
                    android:layout_width="@dimen/top_size"
                    android:layout_height="@dimen/top_size"
                    android:layout_marginLeft="3dp"
                    android:layout_marginRight="5dp"
                    android:padding="5dp" />

                <androidx.appcompat.widget.AppCompatTextView
                    android:id="@+id/title_name"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:layout_gravity="center_vertical"
                    android:textSize="22dp"
                    android:layout_weight="1"/>

                <RadioButton
                    android:id="@+id/radiobt"
                    android:layout_width="100dp"
                    android:layout_height="30dp"
                    android:layout_marginTop="8dp"
                    android:layout_marginBottom="5dp"

                    android:buttonTint="#FF4081"
                    android:scaleX="0.9"
                    android:scaleY="0.9"
                    android:visibility="gone"
                    android:textSize="16dp"
                    android:text="모두 선택"/>


                <ImageView
                    android:id="@+id/photolist_deletecancel"
                    android:layout_width="30dp"
                    android:layout_height="30dp"
                    android:gravity="right"
                    android:src="@drawable/delete_cancel"
                    android:layout_marginTop="7dp"
                    android:layout_marginBottom="5dp"
                    android:layout_marginLeft="7dp"
                    android:layout_marginRight="5dp"
                    android:visibility="gone"/>

                <ImageView
                    android:id="@+id/photolist_deleteok"
                    android:layout_width="30dp"
                    android:layout_height="30dp"
                    android:gravity="right"
                    android:src="@drawable/delete_ok"
                    android:layout_marginTop="7dp"
                    android:layout_marginBottom="5dp"
                    android:layout_marginLeft="7dp"
                    android:layout_marginRight="5dp"
                    android:visibility="gone"/>

                <ImageView
                    android:id="@+id/photolist_delete"
                    android:layout_width="30dp"
                    android:layout_height="30dp"
                    android:gravity="right"
                    android:src="@drawable/ic_delete"
                    android:layout_marginTop="7dp"
                    android:layout_marginBottom="5dp"
                    android:layout_marginLeft="3dp"
                    android:layout_marginRight="5dp" />


            </LinearLayout>

        </androidx.appcompat.widget.Toolbar>
    </com.google.android.material.appbar.AppBarLayout>



    <ImageView
        android:id="@+id/up_button"
        android:layout_width="40dp"
        android:layout_height="40dp"
        android:layout_gravity="start|bottom"
        android:src="@drawable/ic_up"
        android:layout_margin="10dp"
        android:alpha="0.4" />

    <ImageView
        android:id="@+id/down_button"
        android:layout_width="40dp"
        android:layout_height="40dp"
        android:layout_gravity="end|bottom"
        android:src="@drawable/ic_down"
        android:layout_margin="10dp"
        android:alpha="0.4" />

</androidx.coordinatorlayout.widget.CoordinatorLayout>
```

<br>

## 결과

아래와 같이 결과가 잘 출력됨을 알 수 있습니다.

아래와 같은 그리드 레이아웃으로 출력되지는 않을겁니다.  
사용자의 입맛에 맞게 레이아웃을 조정하는 코드와 이미지를 삽입하는 코드를 별도로 작성하셔야 합니다.

![image](https://user-images.githubusercontent.com/57826388/80909210-976bf400-8d61-11ea-86f3-72cd43e488a6.png)

<br>

## 향후계획

바로 이어지는 포스팅에서는 처음 리사이클러뷰에 체크박스를 넣을 때, 숨김으로 처리하고 별도의 이벤트가 발생하면 모두 나타나게하는 로직을 다루도록 하겠습니다.

<br>

*Writer: Jae-Hwan Lee*