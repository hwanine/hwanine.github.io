---
toc: true
toc_sticky: true
categories:
  - Android
title: Android - MVVM, RxJava, Retrofit, Databinding을 통한 앱 서비스 구축, 간단한 프로그램 예제 (Kotlin)
tags: [Kotlin, 코틀린, JAVA, Android, RxJava]
excerpt: "MVVM 아키텍처를 구현하는데 필요한 여러가지 기술요소들을 활용하여 간단한 프로그램 예제를 구현하여 소개합니다."
---

# 개요

## 개요

지금껏 배워온 MVVM, RxJava, Retrofit, Databinding 등 여러가지 기술들을 활용하여 하나의 프로그램을 개발해보도록 하겠습니다.  

마구잡이로 빨리 개발하는 것 보다는, 최대한 구조를 무너뜨리지 않고, View와 ViewModel를 완전히 분리하여 개발해보았습니다.

라이브러리 처리는 생략하고 소스코드 위주로, 하나씩 보여드리며 소개드리도록 하겠습니다. 우선 개발 프로그램에 대한 설명과 구조등을 먼저 설명드리도록 하겠습니다.

<br>

## 프로그램

## 개발 프로그램

우선 우리가 개발할 프로그램은 다음과 같습니다.  
지금 보시는 프로그램은 현재 개발중인 프로그램으로, 관련 기사에 데이터를 가져오는 것 까지 구현하도록 하겠습니다.

![image](https://user-images.githubusercontent.com/57826388/82755696-473bfb00-9e10-11ea-85c0-102db3093c72.png)

<br>

## 구조

아래의 사진은 MVVM 아키텍처를 요약해서 제가 작성한 것입니다.  

![노트1_0(8)](https://user-images.githubusercontent.com/57826388/82759796-73fd0c00-9e2a-11ea-8764-873e3b6dea24.jpg)

<br>

1. View에서 상호작용이 일어납니다.
   
2. View는 ViewModel을 구독하고 있는 상태입니다. 또한, ViewModel에 데이터바인딩이 되어있습니다.
   
3. ViewModel은 뷰의 상호작용에 의해 모델에 필요한 데이터를 요청합니다.(여기서는 Repository를 한번 더 거침)

4. Repository에서 모델에 데이터를 요청해서 가져옵니다. (여기선 RestApi 형태로 가져옴. (Retrofit + RxJava))

5. 가져온 데이터를 ViewModel로 전달합니다.

6. ViewModel에서 데이터를 받아서 View를 렌더링합니다. 여기서 직접적으로 뷰에게 데이터를 전달하지 않습니다.  
   
7. View의 객체는 ViewModel과 데이터 바인딩 되어있기 떄문에 데이터가 변경될 때 마다, ViewModel이 알아서 View를 렌더링해줍니다.

<br>

# 예제

## MainActivity.kt

메인 액티비티는 그냥 뷰 페이저만 만들어줍니다.  
여기서는 Fragment로 활용합니다.

```kotlin
class MainActivity : AppCompatActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        val vpAdapter: VPAdapter =
            VPAdapter(supportFragmentManager)
        viewpager.adapter = vpAdapter
    }
}
```

<br>

```kotlin
class VPAdapter(fm: FragmentManager?) :
    FragmentPagerAdapter(fm!!) {
    private val items: ArrayList<Fragment> = ArrayList()
    override fun getItem(position: Int): Fragment {
        return items[position]
    }

    override fun getCount(): Int {
        return items.size
    }

    init {
        items.add(FragmentSearch())
        items.add(FragmentBoard())
        items.add(FragmentMap())
        items.add(FragmentTreat())
    }
}
```

<br>

```xml
<?xml version="1.0" encoding="utf-8"?>
<layout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools">
    <data>
        <variable name="FragmentSearchBinding" type="com.jhlee.coronabusan.MainActivity" />
    </data>

<LinearLayout
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">


    <com.google.android.material.tabs.TabLayout
        android:id="@+id/tab"
        android:layout_width="match_parent"
        android:layout_height="wrap_content">

        <com.google.android.material.tabs.TabItem
            android:id="@+id/tab_Item1"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="관련 기사" />

        <com.google.android.material.tabs.TabItem
            android:id="@+id/tab_Item2"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="현황판" />

        <com.google.android.material.tabs.TabItem
            android:id="@+id/tab_Item3"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="코로나 맵" />

        <com.google.android.material.tabs.TabItem
            android:id="@+id/tab_Item4"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="대응법" />
    </com.google.android.material.tabs.TabLayout>

    <androidx.viewpager.widget.ViewPager
        android:id="@+id/viewpager"
        android:layout_width="match_parent"
        android:layout_height="match_parent" />
</LinearLayout>
</layout>
```

<br>

## FragmentSearch.kt

Fragment를 데이터 바인딩합니다.  
`ViewModel`도 생성합니다. 필요한 상호작용을 작성합니다.

```kotlin
class FragmentSearch : Fragment() {

    private lateinit var vm: CoronaViewModel

    override fun onCreateView(
        inflater: LayoutInflater, container: ViewGroup?,
        savedInstanceState: Bundle?
    ): View? {
        val binding: FragmentSearchBinding = DataBindingUtil.inflate(inflater, R.layout.fragment_search, container, false)
        vm = ViewModelProvider(this).get(CoronaViewModel::class.java)
        vm.viewInit(binding.fragmentRecycleView)
        vm.getNews()
        vm.uri?.observe(viewLifecycleOwner, Observer { uri ->
            val intent = Intent(Intent.ACTION_VIEW, uri);
            startActivity(intent);
        })

        binding.coronaViewModel = vm
        binding.lifecycleOwner = this
        return binding.root
    }
}
```

<br>

```xml
<?xml version="1.0" encoding="utf-8"?>
<layout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools">
    <data>
        <variable name="CoronaViewModel"
            type="com.jhlee.coronabusan.CoronaViewModel" />
    </data>

    <FrameLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:background="#F0F0F0"
        tools:context=".fragment.FragmentSearch">

        <!-- TODO: Update blank fragment layout -->
        <androidx.recyclerview.widget.RecyclerView

            android:id="@+id/fragment_RecycleView"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:scrollbars="vertical" />

    </FrameLayout>

</layout>
```

<br>

## NewsAdapter.kt

어댑터를 생성합니다. 여기서 뷰 바인딩 되어야하는 객체들은 레이아웃에 전부 뷰모델에 데이터바인딩 되어 있습니다.

```kotlin
class NewsAdapter(viewModel: CoronaViewModel) : RecyclerView.Adapter<NewsAdapter.ViewHolder>() {
    private val viewModel = viewModel

    override fun onCreateViewHolder(parent: ViewGroup, p1: Int): ViewHolder {
        val binding: NewsItemviewBinding = NewsItemviewBinding.inflate(LayoutInflater.from(parent.context), parent, false)
        return ViewHolder(binding)
    }


    override fun getItemCount(): Int = viewModel.getNewsItem().size

    override fun onBindViewHolder(holder: ViewHolder, position: Int) {
        holder.bind(viewModel, position)
    }

    inner class ViewHolder(binding: NewsItemviewBinding) : RecyclerView.ViewHolder(binding.root) {
        val binding = binding

        fun bind(viewModel: CoronaViewModel, pos: Int) {
            binding.pos = pos
            binding.coronaViewModel = viewModel
            binding.executePendingBindings()

        }
    }
}
```

<br>

```xml
<?xml version="1.0" encoding="utf-8"?>
<layout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto">

    <data>
        <variable name="CoronaViewModel"
            type="com.jhlee.coronabusan.CoronaViewModel" />

        <variable
            name="pos"
            type="Integer" />
    </data>

<androidx.coordinatorlayout.widget.CoordinatorLayout

    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:orientation="vertical">

    <androidx.cardview.widget.CardView
        android:id="@+id/card_view"
        android:layout_width="match_parent"
        android:layout_height="100dp"
        android:layout_marginLeft="4dp"
        android:layout_marginRight="4dp"
        app:cardCornerRadius="20dp"
        app:cardUseCompatPadding="true"
        android:onClick="@{() -> CoronaViewModel.toUri(pos)}">

                <LinearLayout
                    android:layout_width="match_parent"
                    android:layout_height="match_parent"
                    android:layout_margin="12dp"
                    android:orientation="vertical">

                    <androidx.appcompat.widget.AppCompatTextView
                        android:id="@+id/news_title"
                        android:text="@{CoronaViewModel.getTitle(pos)}"
                        android:layout_weight="1"
                        android:layout_gravity="top"
                        android:layout_width="wrap_content"
                        android:layout_height="wrap_content"
                        android:textSize="17dp"/>

                    <androidx.appcompat.widget.AppCompatTextView
                        android:id="@+id/news_date"
                        android:text="@{CoronaViewModel.getDate(pos)}"
                        android:textSize="11dp"
                        android:layout_width="wrap_content"
                        android:layout_height="wrap_content" />

                </LinearLayout>

    </androidx.cardview.widget.CardView>


</androidx.coordinatorlayout.widget.CoordinatorLayout>
</layout>
```

<br>

## CoronaViewModel.kt

`getNews()`는 `repository`에 있는 레트로핏 통신 함수를 실행시킵니다. 또한, `RxJava`를 이용하여 바로 값을 받아와서 가공 후 적재합니다.

```kotlin
class CoronaViewModel(application: Application): AndroidViewModel(application){
    private var newsResult: MutableLiveData<ResultGetSearchNews> = MutableLiveData()
    private var newsItem: ArrayList<NewsItems> = arrayListOf()
    private val repo : CoronaRepository = CoronaRepository(application)
    private var newsAdapter = NewsAdapter(this)
    var n = 1

    var uri: MutableLiveData<Uri> = MutableLiveData<Uri>()

    @SuppressLint("CheckResult")
    fun getNews() {

        //newsResult = repo.getNews()
        repo.getNews(n).subscribe(
            { ResultGetSearchNews ->
                for(i in ArrayList(ResultGetSearchNews.items).indices) {
                    if(ResultGetSearchNews.items[i].title.contains("부산") && ResultGetSearchNews.items[i].title.contains("코로나")) {
                        newsItem.add(ResultGetSearchNews.items[i])

                        Handler(Looper.getMainLooper()).post()
                        { newsAdapter.notifyDataSetChanged()} }
                        if(newsItem.size < 30) {
                            n = n + 100
                            getNews()
                        } else
                            n = 1
                    }
                }

            , { throwable -> Log.d("Error!"," ") })

    }

    fun getTitle(pos: Int): Spanned {
        return Html.fromHtml(newsItem.get(pos).title)
    }

    fun getDate(pos: Int): String {
        return dateFormat(newsItem.get(pos).pubDate)
    }

    fun getNewsItem(): List<NewsItems> {
        return newsItem
    }

    fun toUri(pos: Int) {
        uri.setValue(Uri.parse(newsItem.get(pos).originallink))
    }

    fun viewInit(recyclerView: RecyclerView) {
        recyclerView.adapter = newsAdapter
        recyclerView.layoutManager = LinearLayoutManager(getApplication())
    }
    fun dateFormat(str: String): String {

        val formatterCal = SimpleDateFormat("E, dd MMM yyyy HH:mm:ss Z", Locale.US)
        val date: Date = formatterCal.parse(str) // all done

        val formatterStr = SimpleDateFormat("yyyy년 MM월 dd일 (E) / HH:mm:ss", Locale.KOREAN)
        val strDate = formatterStr.format(date)
        return strDate
    }

}
```

<br>

## CoronaRepository.kt

`RxJava`를 이용하여 데이터를 `Observable`로 가져옵니다. 

```kotlin
class CoronaRepository(application: Application) {

    private val api = NaverAPI.create()
    private var newsData: MutableLiveData<ResultGetSearchNews> = MutableLiveData()

    fun getNews(n: Int): Observable<ResultGetSearchNews> = api
        .getSearchNews("코로나 부산", 100, n)
        .subscribeOn(Schedulers.io())
        .observeOn(AndroidSchedulers.mainThread())
}
```

<br>

## NaverAPI

`Retrofit`과 `OkHttp`를 활용하여 API를 초기화 시키고 참조할 인터페이스를 만듭니다.

```kotlin
interface NaverAPI {

    @GET("v1/search/news.json")
    fun getSearchNews(
        @Query("query") query: String,
        @Query("display") display: Int? = null,
        @Query("start") start: Int? = null
    ): Observable<ResultGetSearchNews>

    companion object {
        private const val BASE_URL_NAVER_API = "https://openapi.naver.com/"
        private const val CLIENT_ID = "아이디 입력"
        private const val CLIENT_SECRET = "비밀번호 입력"

        fun create(): NaverAPI {
            val httpLoggingInterceptor = HttpLoggingInterceptor()
            httpLoggingInterceptor.level = HttpLoggingInterceptor.Level.BODY

            val headerInterceptor = Interceptor {
                val request = it.request()
                    .newBuilder()
                    .addHeader("X-Naver-Client-Id", CLIENT_ID)
                    .addHeader("X-Naver-Client-Secret", CLIENT_SECRET)
                    .build()
                return@Interceptor it.proceed(request)
            }

            val client = OkHttpClient.Builder()
                .addInterceptor(headerInterceptor)
                .addInterceptor(httpLoggingInterceptor)
                .build()

            return Retrofit.Builder()
                .baseUrl(BASE_URL_NAVER_API)
                .client(client)
                .addConverterFactory(GsonConverterFactory.create())
                .addCallAdapterFactory(RxJava2CallAdapterFactory.create())
                .build()
                .create(NaverAPI::class.java)
        }
    }
}
```

<br>

## DataModel.kt

```kotlin
data class ResultGetSearchNews(

    var start: Int = 0,
    var display: Int = 0,
    var items: List<NewsItems>
)

data class NewsItems(
    var title: String = "",
    var originallink: String = "",
    var description: String = "",
    var pubDate: String = ""
)
```

<br>

# 결론


![image](https://user-images.githubusercontent.com/57826388/82755696-473bfb00-9e10-11ea-85c0-102db3093c72.png)

지금까지 거대한 MVMM 아키텍처를 간단한 프로그램으로 축소하여 알아보았습니다.

아무래도 전체 프로그램 소스코드를 일일히 설명하기엔 쉽지 않기 떄문에 이해가 안되시는 부분이 있을 수 있습니다.  

그 부분에 대해서는 댓글로 남겨주시면 답변 궁금증을 풀어드리도록 하겠습니다. 감사합니다.