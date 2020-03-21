---
toc: true
toc_sticky: true
categories:
  - Android
title: Android - AAC LiveData (Kotlin)
tags: [Kotlin, 코틀린, JAVA, Android, AAC, LiveData]
excerpt: "AAC LiveData 활용해보기"
---

* **연구주제** : Android - AAC LiveData (Kotlin)
* **연구목적** : 안드로이드에서의 코틀린 활용
* **연구일시** : 2020년 03월 03일 09:00~17:00
* **연구자** : 이재환 <ljh951103@naver.com>
* **연구장비** : HP EliteDesk 800 G4 TWR, Kotlin, Android studio, IntelliJ
* **관련연구** : Java, Android, Kotlin, AAC, LiveData

<br>
   
## 서론

### **LiveData**

`LiveData`란 무엇인가?  
지난 포스팅에서 설명한 AAC(안드로이드 아키텍처 컴포넌트)의 컴포넌트 중의 하나인 LiveData는 `Observable data holder class` 이다.  
즉, 말은 들고 있는 데이터에 대해 관찰을 하는 클래스라고 볼 수 있다.  

이 LiveData는 액티비티, 프래그먼트, 서비스와 같은 안드로이드 컴포넌트의 생명주기에 영향을 받는다.  
이 말은 컴포넌트들의 생명주기가 STARTED, RESUME 같은 활성화 상태일 때만 사용할 수 있다는 말이 된다.

LiveData 객체는 Observer 객체와 함께 사용된다. LiveData가 들고 있는 데이터에 변화가 일어날 경우, LiveData는 등록된 Observer 객체에 변화를 알려주고, Observer의 onChanged() 메소드를 실행하여 데이터를 처리하는 구조로 이루어진다.

<br>
   
## 본론

### **LiveData의 장점**

LiveData가 듣기만 해도 굉장히 유용할 거라는 생각이 든다. 그렇다면 이것의 장점을 보자.

> 1. UI가 데이터 상태와 일치하는 것을 보장한다.

LiveData는 Observer 패턴을 따르며, 생명주기에 변경이 일어날 때마다 Observer 객체에 알려준다.  
그리고 이 Observer 객체를 사용하면 데이터의 변화가 일어나는 곳마다 일일이 UI를 변경하는 코드를 넣을 필요 없이, 통합적이고 확실하게 데이터의 상태와 UI를 일치시킬 수 있다.

<br>

> 2. 메모리 누수가 없다.  

옵저버 객체는 Lifecycle 객체와 결합되어 있어서 컴포넌트가 Destroy 될 경우 메모리를 스스로 해제한다.

<br>

> 3. Stop 상태의 액티비티와 Crash가 발생하지 않는다.  

액티비티가 Back Stack에 있는 것처럼 Observer의 생명주기가 inactive(비활성화) 일 경우, Observer는 LiveData의 어떤 이벤트도 수신하지 않는다.

<br>

> 4. 생명주기에 대한 추가적인 handling을 하지 않아도 된다

LiveData가 생명주기에 따른 Observing을 자동으로 관리를 해주기 때문에 UI 컴포넌트는 그저 관련 있는 데이터를 관찰하기만 하면 된다.

<br>

> 5. 항상 최신 데이터를 유지한다.
   
<br>

> 6. 화면 구성이 변경되어도 데이터를 유지한다.

예를 들어, 디바이스를 회전하여 세로에서 가로로 화면이 변경될 경우에도 LiveData는 회전하기 전의 최신 상태를 즉시 받아온다.

<br>

> 7. Resource를 공유할 수 있다.

LiveData를 확장하는 클래스를 만들어 싱글톤 패턴으로 관리를 할 경우 앱 전체에서 사용 가능한 LiveData 객체를 만들 수 있고, 이를 통해 자원을 공유할 수 있다.

<br>
   
### **LiveData 사용**

LiveData는 ViewModel과 함께 사용되는 경우가 대부분이기 때문에, 예제 코드 또한 ViewModel이 포함된 MVVM 아키텍처 패턴을 기반으로 작성되었다.

````kotlin
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

    ...

}
````

다음과 같이 ViewModel 내부에 LiveData를 달아준다.  

<br>

그리고 이제 LiveData에 Observer를 달아줘야한다.  
여기서 주의할 점이 있는데 되도록이면 onCreate() 메소드 내에 위치시켜주는 것이 바람직하다.

1. 다른 메소드내에 위치할 경우, pause()나 stop()에 의해서 잠시 비활성화된 앱이 다시 활성화가 되면서 LiveData에 대한 코드가 중복 호출될 수 있다. 

2. 액티비티나 프래그먼트가 활성화 되자마자 표시할 수 있는 데이터를 가질 수 있기 때문에 컴포넌트는 STARTED 상태가 되자마자 LiveData 객체로부터 가장 최신의 값을 수신해야 한다.

````kotlin
...

var vm = ViewModelProviders.of(this).get(PhotoViewModel::class.java)
            vm.getTagName().observe(this,
                Observer<List<TagData>> { t -> TagArrayList.addAll(t)
                })

...
````

<br>
   
## 결론

LiveData에 대해 알아보았다. 굉장히 유용한 AAC임을 알 수 있었다.

사실은 Observer은 위의 예와 같이, Activity나 Fragment 내에서 선언하는 것보다 ViewModel 내에서 정의하고 사용하는 것이 더 좋은데 그 이유는 액티비티와 데이터 간의 결합도를 낮추고, UI Controller는 오직 data를 display 하는 것에만 책임을 지게끔 하는 소프트웨어 디자인을 유지하기 위해서입니다.  

다음 시간에는 LiveData와 자주 함께 쓰이는 ViewModel에 대해 알아보겠다.

<br>

## 향후과제

LiveData, ViewModel을 활용한 MVVM 아키텍처 패턴 구현

<br>

## 참고자료

<https://blog.yena.io/studynote/2018/09/08/Android-Kotlin-Room.html>  

<br>

*Writer: Jae-Hwan Lee*


