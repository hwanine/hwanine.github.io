---
toc: true
toc_sticky: true
categories:
  - Android
title: Android - 리사이클러뷰에서 아이템 클릭 리스너 생성 및 처리 (Kotlin)
tags: [Kotlin, 코틀린, JAVA, Android, RecyclerView, Lamda]
excerpt_separator: <!--more-->
---

# Android - 리사이클러뷰에서 아이템 클릭 리스너 생성 및 처리 (Kotlin)
<!--more-->
* **연구주제** : Android - 리사이클러뷰에서 아이템 클릭 리스너 생성 및 처리 (Kotlin)
* **연구목적** : 안드로이드에서의 코틀린 활용
* **연구일시** : 2020년 02월 24일 09:00~17:00
* **연구자** : 이재환 <ljh951103@naver.com>
* **연구장비** : HP EliteDesk 800 G4 TWR, Kotlin, Android studio, IntelliJ
* **관련연구** : Java, Android, Kotlin, RecyclerView, Lamda

<br>
   
## 서론
 
이번 시간에는 리사이클러뷰에서 아이템 클릭 리스너를 사용해보고자 한다.  

![image](https://user-images.githubusercontent.com/57826388/75128896-c2a2e900-5709-11ea-8633-925c089447e1.png)

다음과 같은 화면에서 각각의 이미지를 클릭했을 떄, 각 이미지에 대한 정보를 출력하고자 한다.  
기존의 메인 액티비티 코드에서는 setOnClickListener를 통해 각 item별 처리를 할 수 있었지만,  
리사이클러뷰에서는 별도로 클릭 리스너를 생성해주어야한다.  
이번 포스팅에서는 이를 람다식을 통해 설정해본다.

<br>
   
## 본론

### **어댑터 설정**

````kotlin
class RecyclerAdapterPhoto(val context: FragmentActivity?, var list: List<thumbnailData>, var num: Int, val itemClick: (thumbnailData, Int) -> Unit) :
    RecyclerView.Adapter<RecyclerAdapterPhoto.Holder>()
{
    private var size : Int = 200
    private var padding_size = 200

    inner class Holder(itemView: View?, itemClick: (thumbnailData, Int) -> Unit) : RecyclerView.ViewHolder(itemView!!) {
        //thumbnail_imgview 변수 받아오기

        var thumbnail: ImageView = itemView!!.findViewById<ImageView>(R.id.thumbnail_img)
        var text = itemView?.findViewById<TextView>(R.id.thumbnail_img_text)

        fun bind(data : thumbnailData, num: Int) {
            //photo_view의 내부 값 설정
            val layoutParam = thumbnail.layoutParams as ViewGroup.MarginLayoutParams
            thumbnail.layoutParams.width = size
            thumbnail.layoutParams.height = size
            layoutParam.setMargins(padding_size, padding_size, padding_size, padding_size)
            text!!.text = data.data
            itemView.setOnClickListener { itemClick(data, num) }

        }
    }

    override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): Holder {
        val view = LayoutInflater.from(context).inflate(R.layout.thumbnail_imgview, parent, false)
        return Holder(view, itemClick)
    }

    override fun getItemCount(): Int {
        return list.size
    }

    override fun onBindViewHolder(holder: Holder, position: Int) {
        holder.bind(list[position], position)


    }

    fun setPhotoSize(size : Int, padding_size : Int) {
        this.size = size
        this.padding_size = padding_size
        notifyDataSetChanged()
    }

    fun setThumbnailList(list : List<thumbnailData>) {
        this.list = list
        notifyDataSetChanged()
    }
}
````

다음은 리사이클러뷰 어댑터이다.  
여기서 다음과 같이 지정해준다.

#### **Adapter의 파라미터에 람다식 itemClick을 넣는다.**

![image](https://user-images.githubusercontent.com/57826388/75129034-71472980-570a-11ea-86d1-ee6189cc2594.png)

<br>

#### **Holder에서 클릭에 대한 처리를 할 것이므로, Holder의 파라미터에 람다식 itemClick을 넣는다.**

![image](https://user-images.githubusercontent.com/57826388/75129123-e9155400-570a-11ea-832b-f233969a7e40.png)

<br>

#### **itemView가 클릭됐을 때 처리할 일을 itemClick으로 설정한다. (thumnailData, Int) -> Unit 에 대한 함수는 나중에 RecyclerAdapterPhoto.kt 클래스에서 작성한다.**

![image](https://user-images.githubusercontent.com/57826388/75129235-6771f600-570b-11ea-9a9e-c5f51629679f.png)

<br>

#### **Holder의 파라미터가 하나 더 추가됐으므로, 이곳에도 추가해준다.**

![image](https://user-images.githubusercontent.com/57826388/75129221-4ad5be00-570b-11ea-87c1-e37d5775f7b8.png)

<br>

사진정보인 `thumnailData`와 인덱스 정보인 `Int` 클래스를 파라미터로 사용한다.  
이것은 itemClick 변수를 Adapter의 파라미터로 넣고 어댑터 내에서 setOnClickListener 기능을 설정할 때, (thumnailData, Int) -> Unit에 해당하는 함수 자체를 하나의 변수로 꺼내 쓸 수 있는 것이다.

<br>

### **액티비티에서 호출**

````kotlin
class Main_PhotoView: AppCompatActivity() {
    private var recyclerAdapter : RecyclerAdapterPhoto?= null
    private var thumbnailList = listOf<thumbnailData>()
    private var num: Int = 3
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.main_photoview)
        val view: View = findViewById(R.id.photo_recyclerView)
        setView(view)
        SetHeader()
        setPhotoSize(3, 10)
        // Inflate the layout for this fragment
        var vm = ViewModelProviders.of(this).get(PhotoViewModel::class.java)
        vm.getNameDir().observe(this,
            Observer<List<thumbnailData>> { t -> recyclerAdapter?.setThumbnailList(t) })

    }

    private fun setView(view : View) {
        val recyclerView = view.findViewById<RecyclerView>(R.id.photo_recyclerView)
        recyclerAdapter =
            RecyclerAdapterPhoto(this, thumbnailList, num) {
                thumbnailData, num ->  Toast.makeText(this,"인덱스: ${num} 이름: ${thumbnailData.data}",Toast.LENGTH_SHORT).show()
                val intent = Intent(this, com.example.wimmy.PhotoViewPager::class.java)
                intent.putExtra("photo_num", num)
                intent.putExtra("photo_name", thumbnailData.data)
                startActivity(intent)
            }
        recyclerView?.adapter = recyclerAdapter

        val lm = GridLayoutManager(MainActivity(), 3)
        recyclerView?.layoutManager = lm as RecyclerView.LayoutManager?
    }

    private fun setPhotoSize(row : Int, padding : Int) {
        val displayMetrics = DisplayMetrics()
        this.windowManager.defaultDisplay.getMetrics(displayMetrics)

        var width = displayMetrics.widthPixels
        var size = width / row - 2*padding

        recyclerAdapter!!.setPhotoSize(size, padding)
    }

    private fun SetHeader() {
        val toolbar = findViewById<Toolbar>(R.id.photo_toolbar)
        toolbar.bringToFront()
        setSupportActionBar(toolbar)
        supportActionBar?.setTitle(null)
    }
}
````

<br>

![image](https://user-images.githubusercontent.com/57826388/75129323-d2bbc800-570b-11ea-92ec-bafa97272af1.png)

여기서 우리는 인덱스값과 사진 정보를 넘길 것이기 떄문에 다음과 같이 지정했다.  
이제 실행해보자!

<br>
   
## 결론

<iframe width="724" height="407" src="https://www.youtube.com/embed/lGS64pyizI8" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

<br>

위의 영상을 보면 사진 각각을 클릭했을 때, 각기다른 Toast 메시지가 잘 출력되고있을음 알 수 있다.  
위의 영상에서는 뷰페이저로의 화면전환이 이루어지는데 다음 포스팅에서는 바로 이러한 뷰페이저에 대해 다뤄보도록 하겠다.

<br>

## 향후과제

뷰페이저 구성

<br>

## 참고자료

<https://blog.yena.io/studynote/2017/12/07/Android-Kotlin-RecyclerView2.html>  

<br>

*Writer: Jae-Hwan Lee*


