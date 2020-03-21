---
toc: true
toc_sticky: true
categories:
  - Android
title: Android - 백 스택을 통한 백 버튼 내비게이션 뷰 아이템 활성화 (Kotlin)
tags: [Kotlin, 코틀린, JAVA, Android, BottomNavigationView, BackStack]
excerpt_separator: <!--more-->
---

# Android - 백 스택을 통한 백 버튼 내비게이션 뷰 아이템 활성화 (Kotlin)
<!--more-->
* **연구주제** : Android - 백 스택을 통한 백 버튼 내비게이션 뷰 아이템 활성화 (Kotlin)
* **연구목적** : 안드로이드에서의 코틀린 활용
* **연구일시** : 2020년 02월 21일 09:00~17:00
* **연구자** : 이재환 <ljh951103@naver.com>
* **연구장비** : HP EliteDesk 800 G4 TWR, Kotlin, Android studio, IntelliJ
* **관련연구** : Java, Android, Kotlin, BottomNavigationView, BackStack

<br>
   
## 서론

안드로이드를 개발하면서 문제점이 생겼다.  
우리 앱은 하나의 액티비티가 다섯개의 프레그먼트를 지니고 있고 바텀 내비게이션 뷰로 각 프래그먼트를 활용할 수 있도록 하고 있다.

그러나 프래그먼트로의 이동과 프래그먼트의 기능은 잘 동작하지만 뒤로가기 버튼을 누르면 스택에 쌓이지않고 바로 꺼진다. 사실 이 내용은 저번 프래그먼트 생애주기를 이야기하며 말했었다. 

또한, 백스택을 구현하여 뒤로가기 버튼을 누르면 이전의 프래그먼트가 호출은 잘 되지만 바텀 내비게이션 아이템은 호출이 되지 않았다.

이번에는 이러한 문제점 해결에 대한 글을 다루겠다.

<br>
   
## 본론

### **백스택**

프래그먼트에서 뒤로가기 버튼을 누르게되면 어떻게 될까? 당연히 앱이 종료되게 된다.  
프래그먼트는 기본적으로 백스택이 쌓이지 않기 때문이다. 그래서 우선 우리가 해야할 것은 백스택을 저장하는 코드를 주어야 한다.

```kotlin
override fun onNavigationItemSelected(p0: MenuItem): Boolean {
        val tb: Toolbar = findViewById(R.id.main_toolbar)
        tb.visibility = View.VISIBLE
        val transaction: FragmentTransaction = supportFragmentManager.beginTransaction()

        when(p0.itemId){
            R.id.menu_name ->{
                val fragmentA = NameFragment()
                transaction.replace(R.id.frame_layout,fragmentA, "name")
            }
            R.id.menu_tag -> {
                val fragmentB = TagFragment()
                transaction.replace(R.id.frame_layout,fragmentB, "tag")
            }
            R.id.menu_cal -> {
                val fragmentC = CalFragment()
                transaction.replace(R.id.frame_layout,fragmentC, "cal")
            }
            R.id.menu_location -> {
                val fragmentD = LocationFragment()
                transaction.replace(R.id.frame_layout,fragmentD, "location")
            }
            R.id.menu_map -> {
                val fragmentE = MapFragment()
                transaction.replace(R.id.frame_layout,fragmentE, "map")
                tb.visibility = View.GONE
            }
        }
        transaction.addToBackStack(null)
        transaction.setTransition(FragmentTransaction.TRANSIT_FRAGMENT_FADE)
        transaction.commit()
        return true
    }
```

위의 코드에서 눈여겨 볼 코드는 `addToBackStack(null)`이다.  
이 코드가 바로 백스택에 저장하겠다는 코드다.
'replace' 이후, 'commit' 전에 코드를 삽입해준다.

'addToBackStack(null)'을 호출함으로써 교체 트랜잭션이 백 스택에 저장되므로 사용자는 백 버튼을 눌러 트랜잭션을 되돌리고 이전 프래그먼트를 다시 가져올 수 있다.

하지만 이 방법은 사실 그렇게 옳은 방법은 아니다. 왜일까?  
바로 백스택이 무한정 쌓일 수 있다는 것이다. 이것은 버그로 작용할 수 있다.

이 상태로 1번 프래그먼트와 2번 프래그먼트를 반복하여 눌러보아라. 화면상으로는 멀쩡하지만 사실 스택이 끊임없이 쌓이고 있는 것이다.

유튜브앱에서 하단 프래그먼트를 하나씩 눌러보고 뒤로가기 해보아라. 이전 백스택의 데이터와 중복이 되면 그것을 지워버리기 떄문에 무한정 스택이 쌓이는 일은 발생하지 않는다.

따라서 프래그먼트 생애주기를 더 정교하게 구현할 필요성이 있다. 이번 포스팅에서는 단순하게만 백스택을 적용만 해보았고 더욱 정교한 프래그먼트 생애주기를 다뤄보는 것은 추후 과제로 남기겠다.

<br>

### **바텀 내비게이션 뷰 아이템 활성화**

아무튼 백스택은 정상적으로 구현되었다. 그런데 바텀 내비게이션 뷰의 아이템이 활성화가 안되고 있다! 이제 이것을 구현해보자.

아이디어는 다음과 같다. 

1. 메인 액티비티에서 현재 프래그먼트 정보 불러오기 
2. 프래그먼트 정보로 현재 프래그먼트에 해당하는 바텀 내비게이션 뷰 아이템 활성화

우선 현재 프래그먼트를 어떻게 불러오느냐?  
프래그먼트는 만들어지기전에 태그를 붙일 수 있다.

````kotlin
...
when(p0.itemId){
            R.id.menu_name ->{
                val fragmentA = NameFragment()
                transaction.replace(R.id.frame_layout,fragmentA, "name")
            }
            R.id.menu_tag -> {
                val fragmentB = TagFragment()
                transaction.replace(R.id.frame_layout,fragmentB, "tag")
            }
            R.id.menu_cal -> {
                val fragmentC = CalFragment()
                transaction.replace(R.id.frame_layout,fragmentC, "cal")
            }
            R.id.menu_location -> {
                val fragmentD = LocationFragment()
                transaction.replace(R.id.frame_layout,fragmentD, "location")
            }
            R.id.menu_map -> {
                val fragmentE = MapFragment()
                transaction.replace(R.id.frame_layout,fragmentE, "map")
                tb.visibility = View.GONE
            }
    }
...
````

위의 코드에서 보듯 각 프래그먼트를 'replace' 할 때, 태그를 붙여준다. 다음과 같이 태그를 붙여주면 백스택에 저장될 때, 태그 정보도 함께 저장되어 현재 사용중인 프래그먼트를 참조할 수 있게 되는 것이다.

<br>

### **현재 프래그먼트 참조 및 뷰 활성화**

이제 현재 프래그먼트를 태그를 붙여 삽입했으니 현재 프래그먼트를 찾고 바텀 내비게이션 뷰 아이템을 활성화 해보자.

````kotlin
fun updateBottomMenu(navigation: BottomNavigationView) {
        val tag1: Fragment? = supportFragmentManager.findFragmentByTag("name")
        val tag2: Fragment? = supportFragmentManager.findFragmentByTag("tag")
        val tag3: Fragment? = supportFragmentManager.findFragmentByTag("cal")
        val tag4: Fragment? = supportFragmentManager.findFragmentByTag("location")
        val tag5: Fragment? = supportFragmentManager.findFragmentByTag("map")

        if(tag1 != null && tag1.isVisible()) navigation.getMenu().findItem(R.id.menu_name).setChecked(true)
        else if(tag2 != null && tag2.isVisible()) navigation.getMenu().findItem(R.id.menu_tag).setChecked(true)
        else if(tag3 != null && tag3.isVisible()) navigation.getMenu().findItem(R.id.menu_cal).setChecked(true)
        else if(tag4 != null && tag4.isVisible()) navigation.getMenu().findItem(R.id.menu_location).setChecked(true)
        else if(tag5 != null && tag5.isVisible()) navigation.getMenu().findItem(R.id.menu_map).setChecked(true)
    }
````

위의 코드를 보면 모든 프래그먼트 태그에 해당하는 태그정보를 찾아온다.  
그리고 사용자에게 보여지고 있고, null이 아니면 활성화되게 된다.  

현 프로그램에서는 사용자에게 보여지는 프래그먼트는 하나이기 때문에 충돌하는 일은 없다.

<br>

### **onBackPressed() 재정의 및 적용**

이제 `onBackPressed()`를 재정의하여 여기에다가 함수를 넣어서 적용시켜보자.

````kotlin
override fun onBackPressed() {
        super.onBackPressed()
        val bnv = findViewById<View>(R.id.bottomNavigationView) as BottomNavigationView
        updateBottomMenu(bnv)
    }
````

잘 적용되었는지 확인해보자.

<br>
   
## 결론

<iframe width="1280" height="753" src="https://www.youtube.com/embed/VCRSiehukHw" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

<br>

위의 영상을 보면 코드가 정상적으로 동작하여 올바른 뷰를 출력하고있음을 확인할 수 있다. 

<br>

## 향후과제

- 백스택을 활용해 더욱 정교한 프래그먼트 생애주기를 다뤄보는 것

<br>

## 참고자료

<https://youngest-programming.tistory.com/21>  

<br>

*Writer: Jae-Hwan Lee*






