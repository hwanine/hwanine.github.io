---
toc: true
toc_sticky: true
categories:
  - Android
title: Android - RecyclerView & Checkbox 활용 - 스크롤 시, 체크박스가 해제되는 바인딩 문제 해결 (3) (Kotlin)
tags: [Kotlin, 코틀린, JAVA, Android, Checkbox]
excerpt: "리사이클러뷰에 연결된 뷰 객체 체크박스에 대한 체크가 스크롤 시, 뷰가 바인딩 되면서 풀리는 문제점에 대해 다루겠습니다."
---

# 체크박스 바인딩 이슈

## 개요

일반적으로 체크박스를 리사이클러뷰나 리스트뷰에 구현하게 되면 한 가지 문제점에 부딛힙니다.

![image](https://user-images.githubusercontent.com/57826388/80910047-eb79d700-8d67-11ea-958c-b0fcbb9ae91d.png)

<br>

다음 그림처럼 리사이클러뷰와 리스트뷰는 모든 뷰 객체를 한번에 생성하는게 아니라 화면에 보이는 일부 뷰만을 계속 생성하거나 재활용됩니다.

이 때, 체크박스 경우는 체크를 해제하거나 체크하였더라도 잠시 스크롤을 했다가 다시 오면 그 상태가 남아있지 않고 새로 초기화되어 원래의 상태로 돌아오게 됩니다.

그래서 이 문제를 해결하는 방법에 대해 이번 포스팅에서는 다루어 보겠습니다.

<br>

## 데이터 클래스 추가

우선 체크박스에 체크를 하면 그 체크한 이미지 객체에 대해 현재값을 저장해놓아야 합니다.  
따라서 다음과 같은 데이서 클래스를 작성합니다.

```kotlin
class checkboxData(
    var id: Long,
    var checked: Boolean)
```

<br>

## 데이터 리스트 생성

데이터 클래스를 추가했으면 이제는 체크박스가 체크될 데이터 체크값을 저장할 리스트를 만들어 줍니다.

저는 저의 프로그램 구조상, companion 객체로 만들었고, 기존의 리스트 객체와 별개로 사용합니다.

이 부분은 사용자에 맞게 작성하셔서 사용하시면 될 것 같습니다. 굳이 companion으로 활용하지 않아도 되고 일반 뷰 리스트와 통합으로 사용하셔도 됩니다.

```kotlin
companion object {
        var list = arrayListOf<thumbnailData>()
        var checkboxList = arrayListOf<checkboxData>()
    }
```

<br>

## 어댑터 수정

어댑터의 bind 부분에 다음과 같은 코드를 추가합니다.

이 코드는 뷰가 바인딩 될 때, 체크박스리스트에 객체를 하나씩 추가해줍니다.

```kotlin
if(num >= checkboxList.size)
                checkboxList.add(num, checkboxData(data.photo_id, false))
```

<br>

아래의 코드는 체크박스 리스트에 있는 값을 현재 체크박스로 가져와서 적용합니다.  
그리고 체크박스에 클릭리스너를 달아서 체크박스 리스트의 값에 업데이트합니다.

```kotlin
checkbox.isChecked = checkboxList[num].checked
            checkbox.setOnClickListener {
                if(checkbox.isChecked) {
                    checkboxList[num].checked = true
                }
                else {
                    checkboxList[num].checked = false
                }
            }
```

<br>

> 어댑터 종합

종합하면 어댑터 클래스 코드는 다음과 같습니다.

```kotlin
class RecyclerAdapterPhoto(val context: Activity?, var list: ArrayList<thumbnailData>) :
    RecyclerView.Adapter<RecyclerAdapterPhoto.Holder>()
{
    private lateinit var view: View
    private var ck = 0

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

            if(num >= checkboxList.size)
                checkboxList.add(num, checkboxData(data.photo_id, false))


            thumbnail.setImageResource(0)


            checkbox.isChecked = checkboxList[num].checked


            checkbox.setOnClickListener {
                if(checkbox.isChecked) {
                    checkboxList[num].checked = true
                }
                else {
                    checkboxList[num].checked = false
                }
            }
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
```

<br>

## 결과

이제 앱을 동작시켜보면 체크된 뷰 객체가 스크롤을 하여도 그대로 체크된 상태로 남아있을 것을 확인할 수 있습니다.

<br>

*Writer: Jae-Hwan Lee*