---
toc: true
toc_sticky: true
categories:
  - Android
title: Android - AAC ViewModel (Kotlin)
tags: [Kotlin, 코틀린, JAVA, Android, AAC, LiveData, ViewModel]
excerpt_separator: <!--more-->
---

# Android - AAC ViewModel (Kotlin)
<!--more-->
* **연구주제** : Android - AAC ViewModel (Kotlin)
* **연구목적** : 안드로이드에서의 코틀린 활용
* **연구일시** : 2020년 03월 04일 09:00~17:00
* **연구자** : 이재환 <ljh951103@naver.com>
* **연구장비** : HP EliteDesk 800 G4 TWR, Kotlin, Android studio, IntelliJ
* **관련연구** : Java, Android, Kotlin, AAC, LiveData, ViewModel

<br>
   
## 서론

### **ViewModel을 사용하기 까지..**

원래 안드로이드 프레임워크는 특정 동작이나 제어에서 벗어난 이벤트에 대한 응답으로 해당 UI를 파괴하거나 재생성 하도록 되어있다. 

예를들어서 앱이 사용자의 리스트를 포함하고 있다. 그런데 화면 회전등의 상태 변경으로 재생성이 된다면 새로운 액티비티는 사용자 리스트를 다시 재생성 해야하는 불편함이 있다.  

간단한 데이터의 경우 `onSaveInstanceState()`의 `onCreate()`으로 데이터를 다시 받아서 사용할 수 있지만, 적당한 규모 이상의 데이터라면 다시 재생성하기에는 바람직하지 않다.

또 다른 문제라면 UI 컨트롤러가 리턴을 받기위해 상당시간 소요되는 비동기방식의 call이 빈번하게 일어날 경우이다.  
메모리 누수를 피하기 위해 시스템이 Call들을 정리해야만 한다.  
이런 관리는 많은 유지 관리가 필요하고 상태 변화로 인해 객체를 재 생성하는 경우, 이미 만들어진 객체를 다시 호출해야 하므로 리소스 낭비가 심하다.

UI 컨트롤러는 UI를 보여주고 사용자 이벤트에 반응하고 퍼미션 요청을 처리하는 역할만 처리해야하지, 실제로 DB나 네트워크에서 데이터를 로드하는 등의 작업까지 한다면 클래스가 상당히 무거워져서 바람직하지 않다. 

이런 배경에서 등장한게 바로 ViewModel이다. 그럼 자세히 알아보도록 하자.

<br>
   
## 본론

### **ViewModel 이란?**

`ViewModel` 클래스는 UI 관련 데이터를 저장하고 관리하기 위해 설계되었다.  
ACC에서 ViewModel 이라는 UI 컨트롤러를 위한 헬퍼 클래스를 제공한다. 즉, UI를 위한 데이터를 준비하는 역할을 한다.  
ViewModel객체는 자동으로 화면 회전같은 상태 변화동안 자동으로 유지되고 다음 액티비티 또는 프래그먼트 인스턴스에 즉시 사용가능하다.

![image](https://user-images.githubusercontent.com/57826388/75570497-9a651280-5a9a-11ea-97aa-29750175228b.png)


<https://junghun0.github.io/2019/05/22/android-viewmodel/>

ViewModel에서 ViewModelProvider에 전달된 Lifecycle에 생명주기 범위가 지정된다.  
즉, 주어진 액티비티가 살아있는 동안 메모리에 계속 남아있게된다.
참고로 액티비티의 경우에는 finish될 때, 프래그먼트의 경우에는 detached될 떄, 메모리에서 해제된다.

예를 들어, A 액티비티에서 ViewModel 객체를 생성하면 scope는 A 액티비티의 생명주기에 따릅니다.  
만약 B 액티비티에서 ViewModel에 저장된 값을 재사용하고 싶다면?

B액티비티에서 A의 객체를 다시 가져오면 새 객체가 생성된다. 인스턴스를 받아와도 그것은 안티패턴이다.  
따라서 DataSource나 Repository를 싱글톤으로 유지하는 것이 가장 간결한 방식이 될 것이다.  

<br>

### **활용**

다음 코드는 뷰 모델 뿐만아니라 Repository패턴에 ViewModel을 덜어서 사용하도록 작성되었다.

````Kotlin
class PhotoViewModel(application: Application) : AndroidViewModel(application) {

    private val repo : PhotoRepository = PhotoRepository(application)

    fun Insert(photo : PhotoData) : Long {
       return repo.insert(photo)
    }

    fun Insert(tag : TagData) {
       repo.insert(tag)
    }

    //기본 검색 썸네일
    fun getNameDir() : LiveData<List<thumbnailData>> {
        return repo.getNameDir()
    }
    fun getLocationDir() : LiveData<List<thumbnailData>> {
        return repo.getLocationDir()
    }
    fun getDateInfo(from : Date, to : Date) : String? {
        return repo.getDateInfo(from, to)
    }
    fun getTagDir() : LiveData<List<thumbnailData>> {
        return repo.getTagDir()
    }

    //폴더 선택 시
    fun getNameDir(name : String) : LiveData<List<PhotoData>> {
        return repo.getNameDir(name)
    }
    fun getLocationDir(loc : String) : LiveData<List<PhotoData>> {
        return repo.getLocationDir(loc)
    }
    fun getDateDir(date : Int) : LiveData<List<PhotoData>> {
        return repo.getDateDir(date)
    }
    fun getTagDir(tag : String) : LiveData<List<PhotoData>> {
        return repo.getTagDir(tag)
    }

    ...

}
````

````Kotlin
class PhotoRepository(application: Application) {
   val photoDao : PhotoData_Dao

   companion object {
      private class insertPhotoAsyncTask constructor(private val asyncTask: PhotoData_Dao) : AsyncTask<PhotoData, Void, Long>() {
         override fun doInBackground(vararg params: PhotoData?): Long? {
            return asyncTask.insert(params[0]!!)
         }
      }

      private class insertTagAsyncTask constructor(private val asyncTask: PhotoData_Dao) : AsyncTask<TagData, Void, Void>() {
         override fun doInBackground(vararg params: TagData?): Void? {
            asyncTask.insert(params[0]!!)
            return null
         }
      }

      private class getDateTagAsyncTask constructor(private val asyncTask: PhotoData_Dao) : AsyncTask<Date, Void, String>() {
         override fun doInBackground(vararg params: Date?): String? {
            return asyncTask.getDateInfo(params[0]!!, params[1]!!)
         }
      }

      private class getSizeAsyncTask constructor(private val asyncTask: PhotoData_Dao) : AsyncTask<Void, Void, Int>() {
         override fun doInBackground(vararg params: Void?): Int {
            return asyncTask.getSize()
         }
      }
   }

   init {
      val db = PhotoDB.getInstance(application)!!
      photoDao = db.PhotoData_Dao()
   }

   fun insert(photo : PhotoData) : Long {
      return insertPhotoAsyncTask(photoDao).execute(photo).get()
   }

   fun insert(tag : TagData) {
      insertTagAsyncTask(photoDao).execute(tag)
   }

   fun getNameDir() : LiveData<List<thumbnailData>> {
      return photoDao.getNameDir()
   }
   fun getLocationDir() : LiveData<List<thumbnailData>> {
      return photoDao.getLocationDir()
   }
   fun getDateInfo(from : Date, to : Date) : String? {
       return getDateTagAsyncTask(photoDao).execute(from, to).get()
   }

   ...

}
````

<br>
   
## 결론

ViewModel에 대해 알아보았다. 그저 화면 전환에 제대로 대응하기 위해서도 알아야할 선행지식이 굉장히 많다는걸 볼 수 있다.  
 ViewModel은 프래그먼트 및 수명주기에 대한 깊은 이해가 없어도 사용할 수 있다.  
 이런 관점에서 개발자는 ViewModel을 이용해 해결하고자 하는 문제의 본질에 더 많은 시간을 쏟을 수 있으며 앱 개발에 집중할 수 있다고 생각한다.

<br>

## 향후과제

LiveData, ViewModel을 활용한 MVVM 아키텍처 패턴 구현

<br>

## 참고자료

<https://duzi077.tistory.com/196>  
<https://junghun0.github.io/2019/05/22/android-viewmodel/>
<https://medium.com/@jungil.han/%EC%95%84%ED%82%A4%ED%85%8D%EC%B2%98-%EC%BB%B4%ED%8F%AC%EB%84%8C%ED%8A%B8-viewmodel-%EC%9D%B4%ED%95%B4%ED%95%98%EA%B8%B0-2e4d136d28d2>  

<br>

*Writer: Jae-Hwan Lee*
