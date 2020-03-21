---
toc: true
toc_sticky: true
categories:
  - Android
title: Android - 데이터베이스 Room DB 활용 (Kotlin)
tags: [Kotlin, 코틀린, JAVA, Android, AAC, Room]
thubmnail: "https://user-images.githubusercontent.com/57826388/75561411-510cc700-5a8a-11ea-8d17-c1aba1e3ee29.png"
excerpt_separator: <!--more-->
---

# Android - 데이터베이스 Room DB 활용 (Kotlin)
<!--more-->
* **연구주제** : Android - 데이터베이스 Room DB 활용 (Kotlin)
* **연구목적** : 안드로이드에서의 코틀린 활용
* **연구일시** : 2020년 03월 02일 09:00~17:00
* **연구자** : 이재환 <ljh951103@naver.com>
* **연구장비** : HP EliteDesk 800 G4 TWR, Kotlin, Android studio, IntelliJ
* **관련연구** : Java, Android, Kotlin, AAC, Room

<br>
   
## 서론

### **Room DB**

제목에서 언급한 `Room DB`는 무엇인가?  
룸을 알기 전에 우선 룸이 포함되어 있는 안드로이드 아키텍쳐 컴포넌트라는 것이 있다.  
아키텍쳐는 앱을 견고하고, 실험 가능하고, 유지보수성이 뛰어나도록 만들어주는 라이브러리 모음이다.  
바로 이 중 하나가 룸 데이터베이스이다.

Room은 SQLite의 추상 레이어를 제공하여 SQLite의 객체를 매핑하는 역할을 한다.  
쉽게 말하면 SQLite의 기능을 모두 사용할 수 있고, DB로의 접근을 편하게 도와주는 라이브러리이다.

그럼 SQLite를 바로 사용하지 왜 Room을 사용할까? 그것은 그만한 이유가 있다.  
간단하게 말하면 컴파일 시간을 체크할 수 있고, 무의미한 코드의 반복을 줄여줄 수 있다.  

또한 안드로이드 아키텍처 컴포넌트에 포함되는 만큼, 다른 구성요소인 `LiveData`, `ViewModel` 등의 컴포넌트들을 함께 사용하여 아주 간편하게 DB를 관리하고 UI를 갱신할 수 있다.  
LiveData나 ViewModel은 다음시간에 아키텍쳐 패턴을 구성하면서 설명하도록 하겠다.

<br>
   
## 본론

### **Room 구성요소**

1. Entity: 데이터베이스 안의 테이블을 클래스로 나타낸 것이며 데이터 모델 클래스이다. 

2. DAO: Database Access Object, 데이터베이스에 접근해서 실질적으로 insert, delete 등을 수행하는 메소드를 포함한다.

3. Database: database holder를 포함하며, 앱에 영구 저장되는 데이터와 기본 연결을 위한 주 액세스 지점이다.  
   RoomDatabase를 extend 하는 추상 클래스여야 하며, 테이블과 버전을 정의하는 곳이다.

![image](https://user-images.githubusercontent.com/57826388/75561411-510cc700-5a8a-11ea-8d17-c1aba1e3ee29.png)

<br>

### **구현**

#### **Dependencies**

````kotlin
dependencies {
    implementation "android.arch.persistence.room:runtime:1.1.1"
    kapt "android.arch.persistence.room:compiler:1.1.1"
    kaptTest "android.arch.persistence.room:testing:1.1.1"
}
````

코틀린에서 작성시의 예시이다. 자바는 살짝 다르다.

<br>

#### **Entity**

우리는 사진관리 프로그램을 개발하고 있다.  
따라서 사진과 태그 두 종류의 엔터티를 사용할 것이다.

````kotlin
@Entity(tableName = "photo_data")
class PhotoData(@PrimaryKey(autoGenerate = true) var photo_id: Long,
                @ColumnInfo(name = "name") var name : String,
                @ColumnInfo(name = "file_path") var file_path : String,
                @ColumnInfo(name = "thumbnail_path") var thumbnail_path : String,
                @ColumnInfo(name = "location_info") var location_info : String?,
                @ColumnInfo(name = "date_info") var date_info : Date?,
                @ColumnInfo(name = "favorite") var favorite : Boolean)
````

````kotlin
@Entity(tableName = "tag_data",
    primaryKeys = ["photo_id", "tag"],
    foreignKeys = [ForeignKey(entity = PhotoData::class,
        parentColumns = arrayOf("photo_id"),
        childColumns = arrayOf("photo_id")
    )]
)
class TagData(var photo_id: Long,
              var tag : String,
              @ColumnInfo var type : String):
````

<br>

#### **DAO**

DB에 접근해 질의를 수행할 DAO를 만든다.  
다음과 같이 질의와 함께 정의하였다.

````kotlin
@Dao
interface PhotoData_Dao {
    @Insert(onConflict = REPLACE)
    fun insert(photoData: PhotoData) : Long
    @Insert(onConflict = REPLACE)
    fun insert(tagData: TagData)


    @Update
    fun update(photoData: PhotoData)
    @Update
    fun update(tagData: TagData)

    @Query("DELETE FROM photo_data WHERE photo_id = :photo_id")
    fun deleteById(photo_id : Long)
    @Query("DELETE FROM tag_data WHERE photo_id = :photo_id")
    fun deleteTagById(photo_id: Long)

    @Query("DELETE FROM tag_data WHERE photo_id = :photo_id AND tag = :tag")
    fun delete(photo_id : Long, tag : String)

    @Query("SELECT photo_id as thumbnail_path, file_path as data FROM photo_data WHERE photo_id IN (SELECT MAX(photo_id) FROM photo_data GROUP BY file_path) ORDER BY data")
    fun getNameDir() : LiveData<List<thumbnailData>>
    @Query("SELECT thumbnail_path, location_info as data FROM photo_data WHERE photo_id IN (SELECT MAX(photo_id) FROM photo_data GROUP BY location_info) ORDER BY data")
    fun getLocationDir() : LiveData<List<thumbnailData>>
    @Query("SELECT tag FROM photo_data, tag_data WHERE date_info BETWEEN :from AND :to AND photo_data.photo_id = tag_data.photo_id GROUP BY tag ORDER BY count(*) LIMIT 1")
    fun getDateInfo(from: Date, to : Date) : String
    @Query("SELECT thumbnail_path, tag as data FROM photo_data, (SELECT MAX(photo_id) as photo_id, tag FROM tag_data GROUP BY tag) tag_data WHERE photo_data.photo_id = tag_data.photo_id ORDER BY data")
    fun getTagDir() : LiveData<List<thumbnailData>>

    ...
}
````

<br>

#### **DataBase**

DB에 접근해 질의를 수행할 DAO를 만든다.  
다음과 같이 질의와 함께 정의하였다.

````kotlin
@Database(entities = [PhotoData::class, TagData::class], version = 1)
@TypeConverters(Converters::class)
abstract class PhotoDB: RoomDatabase() {
    abstract fun PhotoData_Dao() : PhotoData_Dao

    companion object {
        private var INSTANCE : PhotoDB? = null

        //singleton patton
        fun getInstance(context: Context) : PhotoDB? {
            if(INSTANCE == null) {
                //synchronized : 중복 방지
                synchronized(PhotoDB::class) {
                    INSTANCE = Room.databaseBuilder(context.applicationContext,
                        PhotoDB::class.java, "photo.db")
                        .fallbackToDestructiveMigration()
                        .build()
                }
            }
            return INSTANCE
        }
    }
````

<br>

#### **DataBase**

이제 액티비티에서 직접 사용할 수 있다.  
먼저 Room은 Thread, AsyncTask 등을 이용해 백그라운드에서 작업해야 한다.  
따라서 데이터베이스의 `Room.dataBuilder`를 호출하여 db 객체를 만들고, 서브 쓰레드에서 데이터 읽기/ 쓰기를 작업하게 한다.  
빈 리스트를 생성하여 Dao에서 만든 메소드를 사용하여 값을 저장하여 사용할 수 있다.

<br>

## 결론

이런식으로 Room을 사용하면 SQLite를 사용할 떄, 수 많은 변수를 정의하는 일이 필요가 없기 때문에 굉장히 편리하다.  
이제는 Room DB를 사용하여 다른 안드로이드 아키텍쳐 컴포넌트를 이용하여 더욱 효율적이고 활용도 높게 사용하는 부분을 다루도록 하겠다.

<br>

## 향후과제

LiveData, ViewModel을 활용한 MVVM 아키텍처 패턴 구현

<br>

## 참고자료

<https://readystory.tistory.com/101>  

<br>

*Writer: Jae-Hwan Lee*
