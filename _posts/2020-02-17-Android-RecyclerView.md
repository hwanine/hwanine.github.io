---
toc: true
toc_sticky: true
categories:
  - Android
title: Android - RecyclerView의 사용법과 원리 (Kotlin)
tags: [Kotlin, 코틀린, JAVA, Android, RecyclerView]
excerpt_separator: <!--more-->
---

# Android - RecyclerView의 사용법과 원리 (Kotlin)
<!--more-->
* **연구주제** : 안드로이드에서 RecyclerView 활용
* **연구목적** : 안드로이드에서의 코틀린 활용
* **연구일시** : 2020년 02월 17일 09:00~17:00
* **연구자** : 이재환 <ljh951103@naver.com>
* **연구장비** : HP EliteDesk 800 G4 TWR, Kotlin, Android studio, IntelliJ
* **관련연구** : Java, Android, Kotlin, RecyclerView

<br>
   
## 서론

### **리사이클러뷰**

`RecyclerView`가 무엇일까? 그냥 리스트뷰와 비슷한 개념이라고 생각하면된다.  
차이가 있다면 리사이클러뷰는 뷰홀더라는 친구를 이용하여 재사용할 수 있어서 리스트뷰보다 훨씬 효율적이다.  
그럼 리사이클러뷰와 리스트뷰는 다음 예시를 보면 이해하기 쉽다.

![image](https://user-images.githubusercontent.com/57826388/74630301-a5b46600-519d-11ea-823b-a2784270ad14.png)

리스트뷰는 다음과 같이 스크롤이 되면서 계속 화면밖의 객체가 삭제되고 다시 화면으로 객체가 생성됨을 반복한다.  

하지만 리사이클러뷰는 객체가 삭제되거나 생성되지않고, 그 화면밖의 뷰 객체를 재활용하여 새로운 화면에 활용한다. 즉, 데이터는 갱신이 되돼, 뷰 객체 자체를 재사용하는 것이다. 객체를 생성하지 않으므로 리스트뷰보다 훨씬 효율성이 높다.

실제 데이터가 100개든 1000개든 원래 만들어놓은 10개정도의 객체만 계속해서 재사용하는 것이 리사이클러뷰이다.

<br>

### **뷰 홀더**

여기서 처음 10개의 뷰 객체를 기억하고있을 객체를 `뷰 홀더`라고 한다.  
이 뷰 홀더가 계속 객체를 재사용하고 데이터를 갱신하면서 사용된다. 그럼 데이터는 어떻게 갱신하는가?

바로 뷰 바인딩이라는 개념을 곧 볼 것이다. 바인딩을 통해 뷰 객체가 계속 업데이트되면서 사용자에게 보여지는 것 이다.

<br>
   
## 본론

그럼 이제 실전코드를 보자!  
다음 코드들은 졸업작품을 위해 현재 개발진행중인 코드이다.

### **Adapter**

````
    inner class Holder(itemView: View) : RecyclerView.ViewHolder(itemView!!) {
        //photo_view 변수 받아오기
        var photo = itemView?.findViewById<ImageView>(R.id.photo)

        fun bind(data : PhotoData) {

            //photo_view의 내부 값 설정
            val layoutParam = photo.layoutParams as ViewGroup.MarginLayoutParams
            photo.layoutParams.width = size
            photo.layoutParams.height = size
            layoutParam.setMargins(padding_size, padding_size, padding_size, padding_size)
        }
    }

    override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): Holder {
        val view = LayoutInflater.from(context).inflate(R.layout.photo_view, parent, false)
        return Holder(view)
    }

    override fun getItemCount(): Int {
        return list.size
    }

    override fun onBindViewHolder(holder: Holder, position: Int) {
        holder.bind(list[position])
    }
 ````

 어댑터는 뷰 홀더를 관리하고 뷰 홀더의 리스트를 리사이클러 뷰에 바인딩 시켜주기위한 작업이 이루어지는 객체이다.  
 말 그대로 리사이클러 뷰와 뷰 홀더의 '어댑터' 라고 생각하면 된다.

<br>

 **1. fun bind(data : PhotoData)**

우리가 개발중인 앱에서는 사진을 보여주기 때문에 
사진에 대한 크기나 여백같은 것들을 조절하여 바인딩하기 위한 메소드다.

<br>

 **2. fun onCreateViewHolder(parent: ViewGroup, viewType: Int): Holder**

뷰 홀더가 생성되는 메소드다. 여기서 뷰 객체를 담고 있는 뷰 홀더를 생성하여 최소한의 뷰 홀더함수만 호출되게 된다.

<br>

 **3. fun getItemCount(): Int**

 사실 굳이 없어도 되는 메소드다. 하지만 뷰 홀더를 구성할 때 반드시 override 되어야 하기 때문에 필요하다고 할 수 있다.  
 리스트에서의 데이터의 개수를 출력한다.

<br>

 **4. fun onBindViewHolder(holder: Holder, position: Int)**

 생성된 뷰 홀더에 데이터를 바인딩 해준다. position 값이 변화하면서 계속적으로 바인딩 되는 것이다.

<br>

### **Main**

````
fun SetView(view : View) {
        recyclerView = view.findViewById<RecyclerView>(R.id.nameRecycleView)
        recyclerAdapter = RecyclerAdapter(activity, photoList)
        recyclerView?.adapter = recyclerAdapter

        val lm = GridLayoutManager(MainActivity(), 3)
        recyclerView?.layoutManager = lm as RecyclerView.LayoutManager?
    }
 ````

이제 리사이클러뷰에다 위에서 구현한 어댑터의 매개변수로 데이터를 넣고 연결하면 뷰 홀더와 연결이 된 것이다.  
실행해보자.

![image](https://user-images.githubusercontent.com/57826388/74631813-40fb0a80-51a1-11ea-9cbe-783bd8c0e7bc.png)

다음과 같이 데이터가 잘 연결되어 출력됨을 볼 수 있다.  
로그를 찍어보면 아마 리스트뷰에 비해 리사이클러뷰가 얼마나 효율적인지 알 수 있을 것이다.

실제로 큰 데이터셋을 이용할 경우, 리스트뷰와 리사이클뷰의 성능차이는 더욱 심해져 확연하게 드러나기 때문에 눈에 띄기 확인할 수 있다.

<br>

## 결론

이번 졸업작품을 준비하면서 리사이클러뷰를 활용해보았다. 사실 이전에 리스트뷰로 뷰 홀더를 연결하는 방법은 많이 사용해보았는데 리사이클러뷰는 처음이다.  

성능이 중요한 앱이기 때문에 선택한 뷰이지만 실제로 써보니 굉장히 편리하고 자유도도 높고 만족감이 높았다.  

다음 포스팅에서는 위의 결과사진에서 하단에보면 하단바가 하나 있다. 이것을 하단 네비게이션 뷰라고 하는데 이것을 구성하는 방법과 원리에 대해 다루겠다.

<br>

## 향후과제

Fragment를 활용한 안드로이드 프로그래밍

<br>

## 참고자료

<https://teamnovatech.co.kr/pageread/KR/477>
<https://wooooooak.github.io/android/2019/03/28/recycler_view/>  


<br>

*Writer: Jae-Hwan Lee*





