---
layout: post
title: Android - 여러 개의 입력을 받는 Custom Dialog 만들기 (2)(Kotlin)
feature-img: "assets/img/pexels/computer.jpeg"
thumbnail: "https://user-images.githubusercontent.com/57826388/76698025-65370200-66e1-11ea-95b8-8a7a7f34a699.png"
tags: [Kotlin, 코틀린, JAVA, Android, Dialog]
author: Jae-Hwan Lee
excerpt_separator: <!--more-->
---

# Android - 여러 개의 입력을 받는 Custom Dialog 만들기 (2) (Kotlin)
<!--more-->
* **연구주제** :Android - 여러 개의 입력을 받는 Custom Dialog 만들기 (Kotlin)
* **연구목적** : 안드로이드에서의 코틀린 활용
* **연구일시** : 2020년 03월 30일 09:00~17:00
* **연구자** : 이재환 <ljh951103@naver.com>
* **연구장비** : HP EliteDesk 800 G4 TWR, Kotlin, Android studio, IntelliJ
* **관련연구** : Java, Android, Kotlin, Dialog

<br>

## 서론

저번 시간에 여러개의 입력을 받는 커스텀 다이얼로그의 레이아웃을 만들어보았다.  

![image](https://user-images.githubusercontent.com/57826388/76698023-610ae480-66e1-11ea-967e-e52889ba6585.png)

![image](https://user-images.githubusercontent.com/57826388/76698025-65370200-66e1-11ea-95b8-8a7a7f34a699.png)

<br>

이번에는 위와 같이 1개에서 5개까지 추가로 입력받을 수 있고, 저장 및 불러오기도 가능한 다이얼로그 클래스를 구현해 볼 것이다.

<br>

## 본론

### **CustomDialog 클래스**

이제 만든 레이아웃을 토대로 다이얼로그를 만들어야 한다.

<br>

#### **onCreateDialog**

다음은 다이얼로그 클래스와 다이얼로그를 생성하는 메소드를 담고 있다.

````
class tagInsertDialog(context: Context, v: View, vm: PhotoViewModel, index: Int, tag_name : AppCompatTextView): DialogFragment() {

    private val contextdlg = context
    private val v = v
    private val vm = vm
    private val index = index
    private val tag_name = tag_name
    @RequiresApi(Build.VERSION_CODES.N)

    override fun onCreateDialog(savedInstanceState: Bundle?): Dialog {
        vm.getTags(this, v, Main_PhotoView.list[index].photo_id)
        val dlgBuilder: androidx.appcompat.app.AlertDialog.Builder = androidx.appcompat.app.AlertDialog.Builder(
            contextdlg,  android.R.style.Theme_DeviceDefault_Light_Dialog_NoActionBar_MinWidth)
        dlgBuilder.setTitle("태그 삽입");
        dlgBuilder.setMessage("삽입할 사진의 특징을 입력해주세요. \n태그를 수정하거나 삭제할 수도 있습니다.")
        dlgBuilder.setIcon(R.drawable.ic_tag);
        dlgBuilder.setCancelable(false);
        dlgBuilder.setView(v)
        val dlg = dlgBuilder.create()
        insert_tag_click(v, dlg)
        return dlg
    }
````

<br>

#### **insert_tag_click**

클릭 리스너를 모아놓은 메소드도 하나 만들자.

````
private fun insert_tag_click(view: View, dlg: androidx.appcompat.app.AlertDialog) {
        tag_addRemove(view)
        insert_saveCancel(view, dlg)
    }
````

<br>

#### **tag_addRemove**

여기서는 다이얼로그의 `+ -` 조작에 대한 리스너를 담는다.

````
private fun tag_addRemove(view: View) {
        view.tag1_add.setOnClickListener{
            view.tag1_add.visibility = View.INVISIBLE
            view.tag2.visibility = View.VISIBLE
        }
        view.tag2_add.setOnClickListener{
            view.tag2_add.visibility = View.INVISIBLE
            view.tag3.visibility = View.VISIBLE
            view.tag2_remove.visibility = View.INVISIBLE
        }
        view.tag3_add.setOnClickListener{
            view.tag3_add.visibility = View.INVISIBLE
            view.tag4.visibility = View.VISIBLE
            view.tag3_remove.visibility = View.INVISIBLE
        }
        view.tag4_add.setOnClickListener{
            view.tag4_add.visibility = View.INVISIBLE
            view.tag5.visibility = View.VISIBLE
            view.tag4_remove.visibility = View.INVISIBLE
        }

        view.tag5_remove.setOnClickListener{
            view.tag5.visibility = View.GONE
            view.tag4_add.visibility = View.VISIBLE
            view.tag4_remove.visibility = View.VISIBLE
            view.tag5_edit.setText("")
        }
        view.tag4_remove.setOnClickListener{
            view.tag4.visibility = View.GONE
            view.tag3_add.visibility = View.VISIBLE
            view.tag3_remove.visibility = View.VISIBLE
            view.tag4_edit.setText("")
        }
        view.tag3_remove.setOnClickListener{
            view.tag3.visibility = View.GONE
            view.tag2_add.visibility = View.VISIBLE
            view.tag2_remove.visibility = View.VISIBLE
            view.tag3_edit.setText("")
        }
        view.tag2_remove.setOnClickListener{
            view.tag2.visibility = View.GONE
            view.tag1_add.visibility = View.VISIBLE
            view.tag2_edit.setText("")
        }
    }
````

<br>

#### **insert_saveCancel**

저장 및 취소를 누를 시, 행동에 대한 리스너를 정의해준다.

````
private fun insert_saveCancel(view: View, dlg: androidx.appcompat.app.AlertDialog) {
        view.tag_save.setOnClickListener {
            CoroutineScope(Dispatchers.Main).launch {
                CoroutineScope(Dispatchers.Default).async {
                    vm.DeleteTag(Main_PhotoView.list[index].photo_id)
                }.await()
                if (view.tag1_edit.text.toString() != "") {
                    vm.Insert(TagData(Main_PhotoView.list[index].photo_id, view.tag1_edit.text.toString()))
                    vm.setTags(tag_name, Main_PhotoView.list[index].photo_id)
                }
                if (view.tag2_edit.text.toString() != "") {
                    vm.Insert(TagData(Main_PhotoView.list[index].photo_id, view.tag2_edit.text.toString()))
                    vm.setTags(tag_name, Main_PhotoView.list[index].photo_id)
                }
                if (view.tag3_edit.text.toString() != "") {
                    vm.Insert(TagData(Main_PhotoView.list[index].photo_id, view.tag3_edit.text.toString()))
                    vm.setTags(tag_name, Main_PhotoView.list[index].photo_id)
                }
                if (view.tag4_edit.text.toString() != "") {
                    vm.Insert(TagData(Main_PhotoView.list[index].photo_id, view.tag4_edit.text.toString()))
                    vm.setTags(tag_name, Main_PhotoView.list[index].photo_id)
                }
                if (view.tag5_edit.text.toString() != "") {
                    vm.Insert(TagData(Main_PhotoView.list[index].photo_id, view.tag5_edit.text.toString()))
                    vm.setTags(tag_name, Main_PhotoView.list[index].photo_id)
                }
                Toast.makeText(contextdlg, "입력 완료 되었습니다.", Toast.LENGTH_SHORT).show()
                dlg.cancel()
            }
        }

        view.tag_cancel.setOnClickListener{ dlg.cancel() }
    }
````

#### **tagsInit**

다이얼로그를 처음 열 때, DB에서 정보를 가져와 다이얼로그에 할당하는 메소드다.  
이 부분에서는 각자의 DB참조 코드를 사용하면된다.
````
fun tagsInit(view: View, tags: List<String>) {
        if(tags.size >= 1) { view.tag1_edit.setText( tags.elementAt(0)) }
        if(tags.size >= 2) {
            view.tag1_add.performClick()
            view.tag2_edit.setText( tags.elementAt(1))
        }
        if(tags.size >= 3) {
            view.tag2_add.performClick()
            view.tag3_edit.setText( tags.elementAt(2))
        }
        if(tags.size >= 4) {
            view.tag3_add.performClick()
            view.tag4_edit.setText( tags.elementAt(3))
        }
        if(tags.size == 5) {
            view.tag4_add.performClick()
            view.tag5_edit.setText( tags.elementAt(4))
        }

    }
````

<br>

### **다이얼로그 호출**

이제 레이아웃과 다이얼로그 클래스가 모두 작성되었다.  
직접 호출을 해보자.

````
val popupInputDialogView: View = layoutInflater.inflate(R.layout.tag_diaglog, null)
        val dlg: tagInsertDialog = tagInsertDialog(this, popupInputDialogView, vm, index, tag_name)
        dlg.show(supportFragmentManager, "tagInsertDialog")
````

이제 실행 시키면 다이얼로그가 잘 동작할 것이다.

<br>

## 결론

여러개의 입력 값을 받고, 또 여러개의 입력 값을 저장하는 커스텀 다이얼로그를 만들어 보았다.  

처음 하는 경우 낯설 수도 있지만, 하다보면 굉장히 재밌고 편하다는 점을 알 수 있을 것이다.

<br>

## 향후과제

<br>

## 참고자료

<br>

*Writer: Jae-Hwan Lee*

