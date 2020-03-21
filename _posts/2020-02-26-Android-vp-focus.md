---
toc: true
toc_sticky: true
categories:
  - Android
title: Android - 특정 뷰에 포커스 맞추기 (Kotlin)
tags: [Kotlin, 코틀린, JAVA, Android, ViewPager]
excerpt_separator: <!--more-->
---

# Android - 특정 뷰에 포커스 맞추기 (Kotlin)
<!--more-->
* **연구주제** : Android - 특정 뷰에 포커스 맞추기 (Kotlin)
* **연구목적** : 안드로이드에서의 코틀린 활용
* **연구일시** : 2020년 02월 26일 09:00~17:00
* **연구자** : 이재환 <ljh951103@naver.com>
* **연구장비** : HP EliteDesk 800 G4 TWR, Kotlin, Android studio, IntelliJ
* **관련연구** : Java, Android, Kotlin, ViewPager

<br>
   
## 서론

이번 시간에는 특정 뷰에 포커스를 맞추는 부분을 다루겠다.  
저번 시간에 뷰 페이저에 대해 다루었다.  
만약 뷰 페이저에서 여러 페이지를 스와이핑하고 백 버튼으로 뷰 페이저를 빠져나온다면, 변경된 뷰 페이저의 화면이 아니라 클릭했을 때의 뷰 스크롤과 같을 것이다.  
우리는 이것을 해결해보고자 한다.

<br>
   
## 본론

우리가 저번에 뷰 페이저로 들어가기전에 인텐트로 화면전환시 데이터를 넘겼던 것에대해 기억하는가?  

````kotlin
recyclerAdapter =
            RecyclerAdapterPhoto(this, thumbnailList) {
                thumbnailData, num, image -> 
                Toast.makeText(this, "인덱스: ${num} 이름: ${thumbnailData.data}", Toast.LENGTH_SHORT)
                    .show()
                val intent = Intent(this, com.example.wimmy.PhotoViewPager::class.java)
                intent.putExtra("photo_num", num)
                photoList.addAll(thumbnailList)
                Log.d("사이즈", "${photoList.size}")
                intent.putParcelableArrayListExtra("photo_list", photoList)     
                    startActivityForResult(intent, 100)
                
            }
````

그렇다. 우리는 클릭한 해당 사진의 인덱스 값을 넘겨서 뷰 페이저의 해당 사진으로 바로 이동이 가능했다.  
마찬가지로 값을 이전 액티비티로 얻어올 수도 있다.  
위의 코드와 같이 `startActivityForResult(intent, 100)`를 하며 화면전환을 한다.

<br>

````kotlin
viewPager.addOnPageChangeListener(object : ViewPager.OnPageChangeListener {

            override fun onPageScrollStateChanged(state: Int) { }
            override fun onPageScrolled(position: Int, positionOffset: Float, positionOffsetPixels: Int) {
                if(check == false ) {
                    viewPager.setCurrentItem(index, false)
                    check = true
                }
                subimg!!.setImageResource(0)    // 애니메이션
                tb.visibility = View.VISIBLE
                bt.visibility = View.VISIBLE
            }
            override fun onPageSelected(position: Int) {
                check_index = position
                text_name.setText(photoList[position].data)
            }
        })
````

뷰 페이저의 페이지 스크롤 리스너에서 페이지를 스와이프할 때 마다 그 인덱스값을 저장한다.

<br>

````kotlin
override fun onBackPressed() {
        val intent = Intent()
        intent.putExtra("index", check_index)
        setResult(Activity.RESULT_OK, intent)
        finish()
    }
````

다음과 같이 백버튼 리스너를 재정의하여 백버튼을 누를 때, 저장시킨 인덱스를 다시 넘겨주도록 한다.

<br>

````kotlin
override fun onActivityResult(requestCode: Int, resultCode: Int, data: Intent?) {
        super.onActivityResult(requestCode, resultCode, data)
        if (resultCode == Activity.RESULT_OK) {
            when (requestCode) {
                100 -> {
                    val doc = data!!.getIntExtra("index", 0)
                    recyclerView?.scrollToPosition(doc)
                }
            }
        }
    }
````

다시 포토 뷰 액티비티에서  백버튼을 눌렀을 때의 인텐트 값을 받아서 그 값으로 다음과 같이 입력한다.  
`recyclerView?.scrollToPosition(doc)`

이 코드를 입력하게되면 해당 뷰에 맞게 스크롤이 자동조정된다.

<br>
   
## 결론

이제 코드를 실행시켜 테스트해보아라.  
자동적으로 뷰에 맞게 스크롤됨을 확인할 수 있다.

<br>

## 향후과제

뷰 페이저와 상하단바 같이 사용하기

<br>

## 참고자료

<https://sudalkim.tistory.com/4>  

<br>

*Writer: Jae-Hwan Lee*
