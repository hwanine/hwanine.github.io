---
toc: true
toc_sticky: true
categories:
  - Android
title: Android - 뷰가 스와이핑 되는 뷰 페이저 (Kotlin)
tags: [Kotlin, 코틀린, JAVA, Android, ViewPager]
excerpt: "안드로이드 뷰 페이저 구현"
---

* **연구주제** : Android - 뷰가 스와이핑 되는 뷰 페이저 (Kotlin) (Kotlin)
* **연구목적** : 안드로이드에서의 코틀린 활용
* **연구일시** : 2020년 02월 25일 09:00~17:00
* **연구자** : 이재환 <ljh951103@naver.com>
* **연구장비** : HP EliteDesk 800 G4 TWR, Kotlin, Android studio, IntelliJ
* **관련연구** : Java, Android, Kotlin, ViewPager

<br>
   
## 서론
 
이번 시간에는 뷰 페이저를 구성해보고자 한다.  

뷰 페이저는 데이터를 페이지 단위로 표시하고 좌/우 스와이프를 통해 페이지를 전환할 수 있도록 만들어주는 컨테이너라고 볼 수 있다.  
뷰 페이저는 자체적으로 뷰를 그리지는 않고 여러 종류의 뷰를 사용하여 각 뷰페이저의 페이지를 구성한다.  

![image](https://user-images.githubusercontent.com/57826388/75365170-2858c600-5900-11ea-9e63-2e0a5a43dda2.png)

다음과 같이 어댑터에 뷰 페이저를 구성할 뷰를 연결시키게 되면 스와이프 가능한 뷰 페이저가 완성된다.  
그럼 뷰 페이저를 직접 구현하러 가보자.

<br>
   
## 본론

![image](https://user-images.githubusercontent.com/57826388/75128896-c2a2e900-5709-11ea-8633-925c089447e1.png)

우선 우리는 다음과 같은 사진들을 각각 클릭을하면 해당 사진을 가진 뷰페이저로 이동하도록 해야한다.  
그리고 좌/우 스와이프시 할당된 다른 이미지가 출력되게 된다.

<br>

### **인텐트**

우선 저번 포스팅에서 뷰 아이템 클릭에 대해 다루었으니 리스너를 붙이는 부분은 생략하겠다.  
특정 이미지를 클릭하면 해당 뷰페이저가 출력되어야 하니 사진에 대한 정보가 반드시 전달 되어야 한다.  
따라서 다음과 같이 데이터도 같이 전달한다.

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

<br>

### **어댑터**

어댑터를 보기전에 뷰 페이저가 동작하는 방법을 알아야한다.  
방법을 어떻게 설명하지 알아보던 중, 가장 직관적이고 이해하기 쉬운 자료를 찾았다.

![image](https://user-images.githubusercontent.com/57826388/75366202-b84b3f80-5901-11ea-9632-1b5e18658709.png)

<br>

그럼 코드를 보자.

````kotlin
public class PagerRecyclerAdapter(private val context: Context, var list: List<thumbnailData>, var tb: View, var bt: View) : PagerAdapter() {
    private var layoutInflater: LayoutInflater? = null
    private var check: Boolean = false

    override fun isViewFromObject(view: View, `object`: Any): Boolean {
        return view === `object`
    }

    override fun getCount(): Int {
        return list.size
    }

    override fun instantiateItem(container: ViewGroup, position: Int): Any {

        layoutInflater = context.getSystemService(Context.LAYOUT_INFLATER_SERVICE) as LayoutInflater
        val v = layoutInflater!!.inflate(R.layout.photoview_pager, null)
        val image = v.findViewById<View>(R.id.imgView) as ImageView
        val vp = container as ViewPager
        vp.addView(v, 0)
        image.setOnClickListener(object : View.OnClickListener {
            override fun onClick(v: View?) {
                if(check == false) {
                   // Log.d("이건?", tb.toString())
                    tb.visibility = View.GONE
                    bt.visibility = View.GONE
                    check = true
                }
                else {
                    tb.visibility = View.VISIBLE
                    bt.visibility = View.VISIBLE
                    check = false
                }
            }
        })
        return v
    }

    override fun destroyItem(container: ViewGroup, position: Int, `object`: Any) {
        val vp = container as ViewPager
        val v = `object` as View
        vp.removeView(v)
    }

    fun setThumbnailList(list : List<thumbnailData>) {
        this.list = list
        notifyDataSetChanged()
    }
}
````

<br>

우선 뷰 페이저의 동작은 다음과 같다.


여기서 override 되는 메소드들은 다음과 같다.

- getCount(): 전체 페이지수를 반환

- instantiateItem(): 화면에 표시할 페이지 뷰 생성
  
- isViewFromObject(): 페이지 뷰가 내부적으로 관리되는 키 객체와 연관되는지 확인
  
<br>

### **뷰 페이저**

````kotlin
class PhotoViewPager : AppCompatActivity() {
    

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        /*getWindow().setFlags(WindowManager.LayoutParams.FLAG_FULLSCREEN,
            WindowManager.LayoutParams.FLAG_FULLSCREEN);
        val uiOptions = getWindow().getDecorView().getSystemUiVisibility();
        var newUiOptions = uiOptions;*/
        getExtra()
        setContentView(R.layout.photoview_frame)
        subimg = findViewById(R.id.sub_img) as ImageView // 뷰페이저로 넘어올 때, 애니메이션을 위한 눈속임
        subimg!!.setImageResource(R.drawable.loding_image)
        super.onEnterAnimationComplete()
        var vm = ViewModelProviders.of(this).get(PhotoViewModel::class.java)
        vm.getNameDir().observe(this,
            Observer<List<thumbnailData>> { t -> recyclerAdapter?.setThumbnailList(t)
            })

        val view: View = findViewById(R.id.imgViewPager)
        val text_name = findViewById<AppCompatTextView>(R.id.imgView_text)
        val tb = findViewById<View>(R.id.mainphoto_toolbar)
        val bt = findViewById<View>(R.id.bottom_photo_menu)

        setView(view, tb, bt)
        toolbar_text(index, text_name)

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

    }
    
    private fun setView(view: View, toolbar: View, bottombar: View) {

        viewPager = view.findViewById<RecyclerView>(R.id.imgViewPager) as ViewPager
        recyclerAdapter =
            PagerRecyclerAdapter(
                this,
                thumbnailList, toolbar, bottombar
            )

        //Log.d("asd",recyclerAdapter?.getThumbnailList())
        viewPager?.adapter = recyclerAdapter

    }


    fun toolbar_text(position: Int, name: AppCompatTextView){
        name.setText(photoList[position].data)
    }

    fun getExtra(){
        if (intent.hasExtra("photo_num") && intent.hasExtra("photo_list")) {
            index = intent.getIntExtra("photo_num", 0)
            photoList = intent.getSerializableExtra("photo_list") as ArrayList<thumbnailData>
        }
        else {
            Toast.makeText(this, "전달된 이름이 없습니다", Toast.LENGTH_SHORT).show()
        }
    }     
}
````

다음과 같이 뷰 페이저 액티비티를 구성한다.  
간단하게 설명하면 뷰 페이저 어댑터와 연결하여 만들고 인텐트로 넘어온 값을 받아서 `viewPager.setCurrentItem(index, false)`를 통해 해당 아이템으로 뷰 페이저를 이동시킨다.

<br>

### **레이아웃**

그럼 뷰 페이저의 레이아웃을 한번 볼까?  

<br>

#### **메인 뷰페이저**

````xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.coordinatorlayout.widget.CoordinatorLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    tools:context=".PhotoViewPager"
    android:background="#000000">

    <androidx.viewpager.widget.ViewPager

        android:id="@+id/imgViewPager"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        tools:ignore="MissingConstraints">

    </androidx.viewpager.widget.ViewPager>

</androidx.coordinatorlayout.widget.CoordinatorLayout>
````

<br>

#### **내부 뷰페이저**

````xml
<androidx.coordinatorlayout.widget.CoordinatorLayout xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">

    <com.ortiz.touchview.TouchImageView
        android:id="@+id/imgView"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:layout_gravity="center_vertical"
        android:src="@drawable/loding_image"
        android:transitionName="pair_thumb"/>

</androidx.coordinatorlayout.widget.CoordinatorLayout>
````

다음과 같이 메인 뷰페이저와 내부 뷰페이저로 구성한다.  
메인 뷰페이저는 액티비티의 레이아웃이고 내부 뷰페이저는 어댑터에서 데이터 뷰를 구성하는데 사용된다.

<br>
   
## 결론

<iframe width="871" height="490" src="https://www.youtube.com/embed/20TJT4PYPEQ" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

<br>

위의 영상을 보면 코드가 정상적으로 동작하여 올바른 뷰 페이저를 출력하고있음을 확인할 수 있다. 

<br>

## 향후과제

- 뷰 페이저에서 빠져나왔을 때, 마지막으로 출력되고있던 뷰에 초점맞추기

<br>

## 참고자료

<https://docs.microsoft.com/ko-kr/xamarin/android/user-interface/controls/view-pager/>  
<https://recipes4dev.tistory.com/148>

<br>

*Writer: Jae-Hwan Lee*
