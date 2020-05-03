---
toc: true
toc_sticky: true
categories:
  - Android
title: Android - RecyclerView & Checkbox 활용 - RadioButton을 활용한 체크박스 전체 선택, 해제 (4) (Kotlin)
tags: [Kotlin, 코틀린, JAVA, Android, Checkbox]
excerpt: "리사이클러뷰에 연결된 뷰 객체들의 체크박스들을 모두 체크하고 해제하는 라디오버튼을 구현해보도록 하겠습니다."
---

# RadioButton - 체크박스 전체 선택

## 개요

체크박스를 체크 및 해제하는 뷰가 필요할 경우, 보통의 프로그램은 일반적으로 전체 선택이나 전체 해제하는 옵션도 포함되어있습니다.  
그럼 이 부분을 어떻게 처리할까요?

> 리사이클러뷰 객체들이 바인딩 될 때 마다 체크?

이 방법을 사용하면 겉으로는 모든 체크박스가 체크 된 것 처럼 보입니다. 하지만 실제로는 바인딩 된 객체들의 체크박스만 보인 것이며, 체크된 객체 전체가 제어되지 않습니다.

<br>

> 별도 체크박스 리스트를 전체 체크/해제

이 방법이 무식한 방법이긴 하지만 이게 가장 최선입니다. 다만 성능에 큰 영향이 있을 수 있기 때문에 쓰레드 처리가 별도로 필요하다고 생각됩니다.

<br>

## 라디오 버튼

### 레이아웃

라디오 버튼을 먼저 만들어야 합니다.  
다음과 같이 생성해줍니다.

```xml
 <RadioButton
                    android:id="@+id/radiobt"
                    android:layout_width="100dp"
                    android:layout_height="30dp"
                    android:layout_marginTop="8dp"
                    android:layout_marginBottom="5dp"

                    android:buttonTint="#FF4081"
                    android:textSize="16dp"
                    android:text="모두 선택"/>
```

라디오 버튼을 레이아웃에 추가하고 한번 실행시켜보세요.  
한 가지 문제점이 있습니다. 라디오버튼은 해제가 되질 않습니다. 원래 라디오버튼은 라디오그룹으로 구현되어 여러개의 라디오 버튼중 하나를 클릭하면 나머지가 해제되는 방식으로 동작합니다.

그래서 이 부분은 개별적으로 추가로 작성해주어야 하는 코드입니다.

<br>

### 액티비티

우선 라디오버튼을 제어할 변수가 하나 필요하니 선언 및 초기화를 해줍니다.

```kotlin
var radiobtck: Boolean = false
```

<br>

그 이휴, 다음과 같이 라디오버튼이 있는 액티비티에서 함수를 생성해 줍니다.  
다음의 코드는 라디오버튼 현재값을 저장하여 이에따라 체크 및 해제가 동작합니다.

```kotlin
private fun btck2() {
        if(radiobtck == true) {
            radiobt.isChecked = false
            radiobtck = false
        }
        else {
            radiobtck = true
        }
    }
```

<br>

이 함수내에 전체 체크, 해제 함수도 등록합니다.  
그럼 이제 어댑터에 setCheckAll을 만들러 가봅시다.

```kotlin
private fun btck2() {
        if(radiobtck == true) {
            recyclerAdapter.setCheckAll(false)
            radiobt.isChecked = false
            radiobtck = false
        }
        else {
            recyclerAdapter.setCheckAll(true)
            radiobtck = true
        }
    }
```

<br>

## 리스트 값 받기, 함수 추가

이제 좀 전에 호출한 함수를 작성해줍니다.  
처음에 말씀드렸듯이, 양이 많아지면 성능에 큰 문제가 발생할 수 있으므로, 쓰레드로 비동기프로그래밍으로 처리하는게 좋습니다.

여기서 `checkboxList`는 연결한 어댑터가 생성하여 동작할 때, 기존 리스트와 동시에 삽입됩니다.

```kotlin
fun setCheckAll(boolean: Boolean) {
        for(ckbox in checkboxList){
            if(ckbox.checked == !boolean)
                ckbox.checked = boolean
        }
        MainHandler.post{ notifyDataSetChanged() }
    }
```

<br>

즉, 이러한 함수를 어댑터에 생성하여 처음 리사이클러뷰가 실행될 때, 출력되어야할 값들을 모두 리스트에 담아줍니다.

```kotlin
fun addThumbnailList(data : thumbnailData) {
        list.add(data)
        checkboxList.add(checkboxData(data.photo_id, false))
    }
```

<br>

## 결과

체크박스 전체 선택, 전체 해제가 제대로 동작하는 것을 알 수 있습니다.

이제 마지막으로 다음 포스팅에서는 체크된 체크박스의 객체들을 어떻게 처리할 것인가 다뤄보겠습니다.






