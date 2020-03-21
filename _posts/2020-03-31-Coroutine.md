---
toc: true
toc_sticky: true
categories:
  - Android
title: Android - 코루틴으로 간단하게 비동기 제어하기 (Kotlin)
tags: [Kotlin, 코틀린, JAVA, Android, Coroutine]
excerpt_separator: <!--more-->
---

# Android - 코루틴으로 간단하게 비동기 제어하기 (Kotlin)
<!--more-->
* **연구주제** : Android - 코루틴으로 간단하게 비동기 제어하기 (Kotlin)
* **연구목적** : 안드로이드에서의 코틀린 활용
* **연구일시** : 2020년 03월 31일 09:00~17:00
* **연구자** : 이재환 <ljh951103@naver.com>
* **연구장비** : HP EliteDesk 800 G4 TWR, Kotlin, Android studio, IntelliJ
* **관련연구** : Java, Android, Kotlin, Coroutine

<br>

## 서론

**코루틴이 무엇인가?**  

코루틴은 동시성 프로그래밍을 가능하게 하도록 만든 개념이다.  
그렇기 때문에 코루틴은 스레드와 많이 비교된다.  

<br>

**코루틴 vs 스레드**

스레드의 경우 자원경쟁, 데드락 등의 문제가 많지만, 코루틴은 별도의 스레드 없이 메인 스레드 상에서 번갈아가며 병렬처리와 유사한 동작을 수행할 수 있다.

스레드는 비동기로, 여러 스레드가 있으면 동시에 실행되는 반면, 코루틴은 이전에 진행중이던 루틴은 정지된다. 즉, 한번에 하나의 코드만 실행된다.  

<br>

**코루틴의 특징**

이처럼 코루틴은 기존의 프로그래밍과는 달리 return을 거치지 않고, 실행 중간에 함수를 빠져 나와 다른 코드를 실행할 수 있고 다시 실행중이던 코드로 이동할 수도 있다.

이러한 코루틴의 방식은 싱글코어에선 효율이 좋지만 멀티코어 프로세서를 활용할 수 없다는 단점이 있다.

<br>

## 본론

**간단한 코루틴 활용**  

만약 비동기 프로그래밍을 사용할 떄, 특정 구간을 제어하고 싶을 때가 있다.  
이 경우, 코루틴을 이용하면 간단한 코드로 해결할 수 있다.

````Kotlin
view.tag_save.setOnClickListener {
                    vm.DeleteTag(Main_PhotoView.list[index].photo_id)
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
````

다음의 코드에서 ,

````Kotlin
vm.DeleteTag(Main_PhotoView.list[index].photo_id)
````

이 코드는 비동기로 동작한다. 그러나 일반적으로 실행하게 되면 아래의 코드와 실행이 겹칠 수 있다.

즉, 개발자의 의도는 삭제 후, 재 삽입이지만 삽입 후 삭제가 될 수도 있다는 말이다.

<br>

이제는 코루틴을 적용해보자.

```kotlin
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
```

 `CoroutineScope(Dispatchers.Main).launch` 는 메인 쓰레드로 할당된다.  
 `CoroutineScope(Dispatchers.Default)`는 서브 쓰레드로 할당되어 메인 쓰레드 안에서 동작한다.  

 여기서 `async`, `await`을 이용하면 해당 코드가 완료될 때까지 기다린다음 다음 코드를 실행시킬 수 있다.  
 즉, 간단하게 비동기코드를 제어해야할 경우, 이렇게 코루틴을 사용한 수 있다.

<br>

## 결론

이렇듯 코루틴의 간단한 개념과 사용을 해보았다.  
비동기로 처리를 할 경우가 대부분이지만, 특정한 경우에 한정하여 다음과 같이 간편하게 코루틴을 적용시킬 수 있다. 

<br>

## 향후과제

<br>

## 참고자료

<http://blog.weirdx.io/post/60785>

<br>

*Writer: Jae-Hwan Lee*

