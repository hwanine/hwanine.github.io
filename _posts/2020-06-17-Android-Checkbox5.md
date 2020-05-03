---
toc: true
toc_sticky: true
categories:
  - Android
title: Android - RecyclerView & Checkbox 활용 - 체크된 체크박스 뷰 객체에 대한 로직 구성 (5) (Kotlin)
tags: [Kotlin, 코틀린, JAVA, Android, Checkbox]
excerpt: "리사이클러뷰에 연결된 체크된 체크박스 뷰 객체에 대한 로직을 구성해보겠습니다."
---

# 체크된 체크박스에 대한 로직

체크된 체크박스들에 대한 로직은 간단합니다. 그저 반복문으로 이전에 체크박스를 전체 체크하는 과정과 유사합니다.

<br>

## 레이아웃

우선 `photolist_deleteok`라는 객체를 레이아웃에 등록합니다.

```xml
<ImageView
    android:id="@+id/photolist_deleteok"
    android:layout_width="30dp"
    android:layout_height="30dp"
    android:gravity="right"
    android:src="@drawable/delete_ok"
    android:layout_marginTop="7dp"
    android:layout_marginBottom="5dp"
    android:layout_marginLeft="7dp"
    android:layout_marginRight="5dp"/>
```

<br>

## 클릭 리스너 정의

이후 위의 객체의 클릭 리스너에 필요한 동작함수를 호출합니다.

```kotlin
photolist_deleteok.setOnClickListener {
            deletePhotoDlg()
        }
```

<br>

## 체크된 객체만 찾아서 동작

deletePhotoDlg()를 정의해줍니다.  
저는 특정 다이얼로그를 먼저 생성하고, 이후 동작하도록 코드를 작성하였습니다.

또한 이미지 삭제에 대한 로직으로 구현하였습니다.  
보시는 코드에서는 실제 이미지 삭제코드는 제외했고 리스트에서 해당 뷰가 지워지게 됩니다. 이 부분은 사용자 알맞게 구성하시면 될 것 같네요.

이 부분은 리스트의 크기만큼 루프가 볼며 객체 내 데이터를 확인하여 연산이 이루어집니다.  

따라서 저번 포스팅의 라디오버튼 전체선택과 같이 리스트의 객체가 많게되면 성능상의 문제가 발생하니 쓰레드를 통한 비동기 프로그래밍을 추천합니다.

```kotlin
 fun deletePhotoDlg() {
        val warningBuilder: androidx.appcompat.app.AlertDialog.Builder =
            androidx.appcompat.app.AlertDialog.Builder(
                this,    // 경고 다이얼로그
                android.R.style.Theme_DeviceDefault_Light_Dialog_NoActionBar_MinWidth
            )
        warningBuilder.setTitle("알림") //제목
        warningBuilder.setMessage("체크된 사진들을 모두 삭제합니다.\n정말 삭제하시겠습니까?") // 메시지
        warningBuilder.setCancelable(false)
        warningBuilder.setPositiveButton(
            "확인",
            DialogInterface.OnClickListener { dialog, which ->
                    var i = 0
                    var j = 0
                    while (checkboxList.size != 0 && i < checkboxList.size) {
                        if (checkboxList[i].checked) {
                            val temp = checkboxList[i].id
                            list.removeAt(i)
                            checkboxList.removeAt(i)                   
                            recyclerAdapter.notifyDataSetChanged()    
                            }
                            j = 1
                        } else
                            i++
                    }
                    if(j==1)
                        Toast.makeText(this, "사진이 삭제 완료 되었습니다.", Toast.LENGTH_SHORT).show()
                    else
                        Toast.makeText(this, "삭제할 사진을 체크해주세요.", Toast.LENGTH_SHORT).show()
                }
            )
        warningBuilder.setNegativeButton(
            "취소",
            DialogInterface.OnClickListener { dialog, which ->
                dialog.cancel()
            })
        val dlgWarning = warningBuilder.create()
        dlgWarning.show()
    }
```

<br>

## 결과

위의 코드를 실행해보면 다음과 같이 다이얼로그가 생성되며, 확인 버튼을 누르게되면 위에서 정의한 로직이 동작합니다.

![image](https://user-images.githubusercontent.com/57826388/80917728-e8004300-8d9b-11ea-8789-305a86ea8f99.png)


<br>

# 결론

지금까지 리사이클러뷰와 체크박스를 함께 다루는 방법들을 소개하였습니다.

리스트뷰 혹은 리사이클러뷰 객체에 체크박스를 두어 제어하는 것이 생각보다는 쉽지 않습니다. 그런만큼 좋은 참고 글이 되었으면 좋겠습니다.

![image](https://user-images.githubusercontent.com/57826388/80917720-dfa80800-8d9b-11ea-8924-456824ad7d47.png)

![image](https://user-images.githubusercontent.com/57826388/80917725-e46cbc00-8d9b-11ea-8cb2-58e10cce0980.png)
