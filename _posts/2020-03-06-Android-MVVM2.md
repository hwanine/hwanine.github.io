---
toc: true
toc_sticky: true
categories:
  - Android
title: Android - RoomDB를 이용한 MVVM 아키텍처 디자인 패턴 구축 (Kotlin) (2)
tags: [Kotlin, 코틀린, JAVA, Android, AAC, LiveData, ViewModel]
excerpt_separator: <!--more-->
---

# Android - RoomDB를 이용한 MVVM 아키텍처 디자인 패턴 구축 (Kotlin) (2)
<!--more-->
* **연구주제** : Android - RoomDB를 이용한 MVVM 아키텍처 디자인 패턴 구축 (Kotlin)
* **연구목적** : 안드로이드에서의 코틀린 활용
* **연구일시** : 2020년 03월 06일 09:00~17:00
* **연구자** : 이재환 <ljh951103@naver.com>
* **연구장비** : HP EliteDesk 800 G4 TWR, Kotlin, Android studio, IntelliJ
* **관련연구** : Java, Android, Kotlin, AAC, LiveData, ViewModel

<br>
   
## 서론

### **MVVM 패턴**

지난 포스팅에서 MVVM 패턴에 대해 설명하였다.  
이번 포스팅에선 직접 안드로이드상에서 RoomDB를 생성하고 MVVM 패턴을 적용시켜 전체적은 구조를 Kotlin으로 구현을 해보자.
   
<br>

## 본론

구조는 다음과 같다.

- PhotoData_Dao.kt
- PhotoDB
- PhotoViewModel
- PhotoRepositiory
- PhotoData

<br>

### **PhotoData_Dao.kt**

DB에 접근해 질의를 수행하는 DAO를 만든다.  
다음과 같이 질의와 함께 정의한다.

````Kotlin
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

    @Query("SELECT * FROM photo_data where file_path = :name")
    fun getNameDir(name : String) : LiveData<List<PhotoData>>
    @Query("SELECT * FROM photo_data where location_info = :loc")
    fun getLocationDir(loc : String) : LiveData<List<PhotoData>>
    @Query("SELECT * FROM photo_data where date_info = :date")
    fun getDateDir(date : Int) : LiveData<List<PhotoData>>
    @Query("SELECT photo_data.* FROM photo_data, tag_data where (photo_data.photo_id = tag_data.photo_id) AND (tag_data.tag = :tag)")
    fun getTagDir(tag : String) : LiveData<List<PhotoData>>

    @Query("SELECT tag_data.photo_id, tag_data.tag, tag_data.type FROM photo_data, tag_data WHERE ((photo_data.file_path = :name) AND (photo_data.photo_id = tag_data.photo_id)) ORDER BY tag")
    fun getNameTag(name : String) : LiveData<List<TagData>>
    @Query("SELECT tag_data.photo_id, tag_data.tag, tag_data.type FROM photo_data, tag_data WHERE ((photo_data.location_info = :loc) AND (photo_data.photo_id = tag_data.photo_id)) ORDER BY tag")
    fun getLocationTag(loc : String) : LiveData<List<TagData>>
    @Query("SELECT tag_data.photo_id, tag_data.tag, tag_data.type FROM photo_data, tag_data WHERE ((photo_data.date_info = :date) AND (photo_data.photo_id = tag_data.photo_id)) ORDER BY tag")
    fun getDateTag(date : Int) : LiveData<List<TagData>>
    @Query("SELECT tag_data.photo_id, tag_data.tag, tag_data.type FROM photo_data, tag_data WHERE ((tag_data.tag = :tag) AND (photo_data.photo_id = tag_data.photo_id)) ORDER BY tag")
    fun getTagTag(tag : String) : LiveData<List<TagData>>

    @Query("SELECT count(*) FROM photo_data")
    fun getSize() : Int
}
````

<br>

### **PhotoDB.kt**

데이터베이스를 정의하고 생성한다. DB 모델에 대한 정보가 들어간다.

````Kotlin
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
}
````

<br>

### **PhotoViewModel.kt**

뷰 모델을 정의한다. 뷰에서 직접 데이터를 참조하기도 하고, 뷰 모델에서 뷰를 감시하기도 한다.

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

    fun getNameTag(name : String) : LiveData<List<TagData>> {
        return repo.getNameTag(name)
    }
    fun getLocationTag(loc : String) : LiveData<List<TagData>> {
        return repo.getLocationTag(loc)
    }
    fun getDateTag(date : Int) : LiveData<List<TagData>> {
        return repo.getDateTag(date)
    }
    fun getTagTag(tag : String) : LiveData<List<TagData>> {
       return repo.getTagTag(tag)
    }

    fun getSize() : Int {
        return repo.getSize()
    }
}
````

<br>

### **PhotoRepository.kt**

뷰에서 데이터를 참조할 떄, 뷰 모델을 먼저 참조하고 뷰 모델이 리파지터리를 통해 데이터를 가져오도록 한다.

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
   fun getTagDir() : LiveData<List<thumbnailData>> {
      return photoDao.getTagDir()
   }

   fun getNameDir(name : String) : LiveData<List<PhotoData>> {
      return photoDao.getNameDir(name)
   }
   fun getLocationDir(loc : String) : LiveData<List<PhotoData>> {
      return photoDao.getLocationDir(loc)
   }
   fun getDateDir(date : Int) : LiveData<List<PhotoData>> {
      return photoDao.getDateDir(date)
   }
   fun getTagDir(tag : String) : LiveData<List<PhotoData>> {
      return photoDao.getTagDir(tag)
   }

   fun getNameTag(name : String) : LiveData<List<TagData>> {
      return photoDao.getNameTag(name)
   }
   fun getLocationTag(loc : String) : LiveData<List<TagData>> {
      return photoDao.getLocationTag(loc)
   }
   fun getDateTag(date : Int) : LiveData<List<TagData>> {
      return photoDao.getDateTag(date)
   }
   fun getTagTag(tag : String) : LiveData<List<TagData>> {
      return photoDao.getTagTag(tag)
   }

   fun getSize() : Int {
      return getSizeAsyncTask(photoDao).execute().get()
   }
}
````

<br>

### **PhotoData.kt**

DB 엔터티나 기타 데이터를 생성하고 정의한다. 

````Kotlin
@Entity(tableName = "photo_data")
class PhotoData(@PrimaryKey(autoGenerate = true) var photo_id: Long,
                @ColumnInfo(name = "name") var name : String,
                @ColumnInfo(name = "file_path") var file_path : String,
                @ColumnInfo(name = "thumbnail_path") var thumbnail_path : String,
                @ColumnInfo(name = "location_info") var location_info : String?,
                @ColumnInfo(name = "date_info") var date_info : Date?,
                @ColumnInfo(name = "favorite") var favorite : Boolean)

@Entity(tableName = "tag_data",
    primaryKeys = ["photo_id", "tag"],
    foreignKeys = [ForeignKey(entity = PhotoData::class,
        parentColumns = arrayOf("photo_id"),
        childColumns = arrayOf("photo_id")
    )]
)

class TagData(var photo_id: Long,
              var tag : String,
              @ColumnInfo var type : String)

data class thumbnailData( var thumbnail_path: String,
                             var data : String )
````

<br>

### **연결**

다음과 같이 뷰에서 뷰 모델과 연결한다.

````Kotlin
var vm = ViewModelProviders.of(this).get(PhotoViewModel::class.java)
            vm.getTagDir().observe(this,
                Observer<List<PhotoData>> { t -> PhotoList =
                    recyclerAdapter?.setThumbnailList(t)!!
                })
````

<br>

## 결론

이렇게 MVVM 패턴을 구축할 수 있다.  
DB에 대한 클래스와 MVVM 구조만 잘 정의해놓으면 실제로 액티비티에서는 위의 예제와 같이 단순히 뷰 모델과 연결함으로써 추가적인 번거로움 없이 편하게 관리할 수 있다.
 
<br>

## 향후과제

-

<br>

## 참고자료

<https://wonsohana.wordpress.com/2019/05/14/android-mvvm-%EC%9D%84-%EC%A0%95%EB%A6%AC%ED%95%B4%EB%B3%B4%EC%9E%90/>  
<https://blog.yena.io/studynote/2019/03/16/Android-MVVM-AAC-1.html>

<br>

*Writer: Jae-Hwan Lee*

