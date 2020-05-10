---
toc: true
toc_sticky: true
categories:
  - Android
title: Android - Retrofit2, OkHttp를 함께 사용하여 더욱 간결한 RestAPI 연동 (Kotlin) (2)
tags: [Kotlin, 코틀린, JAVA, Android, RestAPI, Retrofit]
excerpt: "Retrofit과 OkHttp를 함께 사용하여 더욱 간결하게 네이버 RestAPI와의 연동하는 과정을 소개하겠습니다."
---

# Retrofit2을 활용한 RestAPI 연동

## 개요

### **OkHttp**

이전 포스팅에서 레트로핏을 사용하는 방법에 대해 다루었습니다.

그러나 요즘은 실제로 레트로핏을 더 편리하게 쓰기 위해 흔히 OkHttp를 함께 사용합니다.

`OkHttp`는 Http를 더 간편하고 효율적으로 쓸 수 있게 도와주는 클라이언트입니다.  
이 예제에서는 입력값이나 서버 응답을 로그로 쉽게 확인하기 위해서, 또 헤더 값을 편하게 입력하기 위해서 OkHttp를 사용합니다.  

또한 이전 포스팅에서 다루엇던 레트로핏은 실제로 액티비티마다 레트로핏 객체를 생성하고, API의 각 함수마다 헤더 값을 달아주어야 했습니다.  

이번 포스팅에서는 Retrofit과 OkHttp를 함께 사용하며, 이러한 코드를 조금 더 정리해서 간결하게 사용해 보겠습니다.

<br>

## 네이버 API 설정

***지난 포스팅을 보시고 이미 선행하신 부분이 있다면 그 부분은 넘기셔도 괜찮습니다.***

<br>

우선 네이버 개발자 등록 후, 사용할 API를 설정해야 합니다.

다음 네이버 개발자 페이지로 가서 로그인 후, 앱을 등록합니다.

<https://developers.naver.com/main/>

<br>

앱을 등록하고 필요한 API를 설정합니다.  
예제에서는 검색과 파파고 번역 API를 사용합니다.

![image](https://user-images.githubusercontent.com/57826388/81490663-f11f7180-92bf-11ea-9317-4970f2289333.png)

<br>

이후 클라이언트 ID와 비밀번호를 기억해둡니다.  
이제 모든 준비는 끝났습니다.

![image](https://user-images.githubusercontent.com/57826388/81490649-d2b97600-92bf-11ea-9a78-ef98feea83fd.png)

<br>

## 초기 설정

우선 앱 수준 그래들에 다음과 같이 기존 Retrofit과 OkHttp에 관련된 라이브러리를 추가해줍니다.

```Gradle
implementation 'com.squareup.retrofit2:converter-gson:2.6.2' 
implementation 'com.squareup.retrofit2:retrofit:2.6.0'
implementation 'com.squareup.okhttp3:okhttp:3.11.0'
implementation 'com.squareup.okhttp3:logging-interceptor:3.11.0'
```

<br>

그리고 매니페스트에 인터넷 접속 권한을 허용합니다.

```XML
<uses-permission android:name="android.permission.INTERNET"/>
```

<br>

## RestAPI 통신

> 데이터 클래스 생성

```Kotlin
// ResultGetSearchNews.kt

data class ResultGetSearchNews(
    var lastBuildDate: String = "",
    var total: Int = 0,
    var start: Int = 0,
    var display: Int = 0,
    var items: List<Items>
)

data class Items(
    var title: String = "",
    var originallink: String = "",
    var link: String = "",
    var description: String = "",
    var pubDate: String = ""
)
```

```Kotlin
// ResultTransferPapago.kt

data class ResultTransferPapago (
    var message: Message
)

data class Message(
    var result: Result
)

data class Result (
    var srcLangType: String = "",
    var tarLangType: String = "",
    var translatedText: String = ""
)
```

<br>

> API 인터페이스 생성

create() 함수를 Activity에서 Interface인 `NaverAPI.kt`으로 옮겼으며, 상수들을 모두 `Companion` 처리하여 전역으로 둡니다.

```Kotlin
// NaverAPI.kt

companion object {
    private const val BASE_URL_NAVER_API = "https://openapi.naver.com/"
    private const val CLIENT_ID = "네이버_개발자센터_아이디"
    private const val CLIENT_SECRET = "네이버_개발자센터_비밀번호"

    fun create(): NaverAPI {
        return Retrofit.Builder()
            .baseUrl(BASE_URL_NAVER_API)
            .addConverterFactory(GsonConverterFactory.create())
            .build()
            .create(NaverAPI::class.java)
    }
}
```

<br>

이제 로그 기록에 사용할 `HttpLoggingInterceptor`와 고정 헤더 값에 사용할 `Interceptor`을 만들어줍니다.  
두 개의 `Interceptor`를 클라이언트에 추가한 후, 다시 이 클라이언트를 레트로핏을 빌드할 때 추가해줍니다.  
create 할 때 헤더를 고정으로 넣어주므로 호출할 때에 사용하던 헤더는 필요가 없으므로 지워줍니다.

```Kotlin
// NaverAPI.kt

interface NaverAPI {
    @GET("v1/search/news.json")
    fun getSearchNews(
        @Query("query") query: String,
        @Query("display") display: Int? = null,
        @Query("start") start: Int? = null
    ): Call<ResultGetSearchNews>

    @FormUrlEncoded
    @POST("v1/papago/n2mt")
    fun transferPapago(
        @Field("source") source: String,
        @Field("target") target: String,
        @Field("text") text: String
    ): Call<ResultTransferPapago>

    companion object {
        private const val BASE_URL_NAVER_API = "https://openapi.naver.com/"
        private const val CLIENT_ID = "네이버_개발자센터_아이디"
        private const val CLIENT_SECRET = "네이버_개발자센터_비밀번호"

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
                .build()
                .create(NaverAPI::class.java)
        }
    }
}
```

<br>

> API 호출 및 연동

```Kotlin
// MainActivity.kt

val api = NaverAPI.create()

api.getSearchNews("테스트").enqueue(object : Callback<ResultGetSearchNews> {
    override fun onResponse(
        call: Call<ResultGetSearchNews>,
        response: Response<ResultGetSearchNews>
    ) {
        // 성공
    }

    override fun onFailure(call: Call<ResultGetSearchNews>, t: Throwable) {
        // 실패
    }
})
```

<br>

## 결론

이전 포스팅에서 구현했던 것 보다 훨씬 간결하고 여러 액티비티에서 쉽게 사용할 수 있도록 구현된 것을 확인할 수 있습니다.

<br>

- 참고자료: <https://blog.yena.io/studynote/2020/01/08/Android-Kotlin-Retrofit.html>

