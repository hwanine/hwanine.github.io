---
layout: post
title: Android - Activity에서 Parcelable을 이용해 ArrayList를 전달하고 받아오기 (Kotlin)
feature-img: "assets/img/pexels/computer.jpeg"
tags: [Kotlin, 코틀린, JAVA, Android, Activity, Intent, Parcelable]
author: Jae-Hwan Lee
excerpt_separator: <!--more-->
---

# Android - Activity에서 Parcelable을 이용해 ArrayList를 전달하고 받아오기 (Kotlin)
<!--more-->
* **연구주제** : Android - Activity에서 Parcelable을 이용해 ArrayList를 전달하고 받아오기 (Kotlin)
* **연구목적** : 안드로이드에서의 코틀린 활용
* **연구일시** : 2020년 03월 23일 09:00~17:00
* **연구자** : 이재환 <ljh951103@naver.com>
* **연구장비** : HP EliteDesk 800 G4 TWR, Kotlin, Android studio, IntelliJ
* **관련연구** : Java, Android, Kotlin, Activity, Intent, Parcelable

<br>

## 서론

앱 개발을 하면서 리스트를 넘겨줘야하는 상황이 왔다.  
하지만 액티비티로 데이터를 넘길 때, List 자료형은 데이터 전달이 불가능했다.
DB를 구축할 떄, List 형식으로 데이터를 구성했기 때문에 변경도 마땅치 않았기 때문에, 바로 ArrayList로 변환 후 객체로서 전달하려고 한다. 

ArrayList는 `Parcelable` 를 이용하면 다른 액티비티로 전달할 수 있다.  
Parcelable은 인터페이스이며, Parcel에 객체를 write/read 하도록 만들어준다.  
그럼 ArrayList는 어떻게 전달하면 좋을지 알아보자.

<br>

## 서론

### **putParcelableArrayListExtra**

다음 메소드를 사용해서 다음과 같이 데이터를 넘겨준다.

````
private var PhotoList = arrayListOf<PhotoData>()
intent.putParcelableArrayListExtra("photo_list", PhotoList)
````

하지만 문제가 생겼다.    
이렇게 입력하면 빨간줄이 뜨면서 에러가 난다.  
우리는 한 가지 일을 더 해주어야한다.

<br>

### **Parcelable 구현**

*우선 우리는 PhotoData 라는 데이터 클래스를 따로 만들어주었기 떄문에 이를 예로 설명하겠다.*

리스트를 전달하려면 리스트에 담긴 데이터에서 `Parcelable`를 구현해야한다.

````
@Entity(tableName = "photo_data")
class PhotoData(@PrimaryKey var photo_id: Long,
                @ColumnInfo(name = "name") var name : String,
                @ColumnInfo(name = "file_path") var file_path : String,
                @ColumnInfo(name = "location_info") var location_info : String?,
                @ColumnInfo(name = "date_info") var date_info : Date?,
                @ColumnInfo(name = "favorite") var favorite : Boolean): Parcelable {
    constructor(parcel: Parcel) : this(
        parcel.readLong(),
        parcel.readString().toString(),
        parcel.readString().toString(),
        parcel.readString().toString(),
        parcel.readDate(),
        parcel.readByte() != 0.toByte()
    )


    override fun writeToParcel(parcel: Parcel, flags: Int) {
        parcel.writeLong(photo_id)
        parcel.writeString(name)
        parcel.writeString(file_path)
        parcel.writeString(location_info)
        parcel.writeDate(date_info)
        parcel.writeByte(if (favorite) 1 else 0)
    }

    override fun describeContents(): Int {
        return 0
    }

    companion object CREATOR : Parcelable.Creator<PhotoData> {
        override fun createFromParcel(parcel: Parcel): PhotoData {
            return PhotoData(parcel)
        }

        override fun newArray(size: Int): Array<PhotoData?> {
            return arrayOfNulls(size)
        }
    }
}
````

<br>

여기서 Date 형식으로 된 `date_info`의 경우 입/출력시 변환을 해주어야 한다.

````
fun Parcel.writeDate(date: Date?) {
    writeLong(date?.time ?: -1)
}

fun Parcel.readDate(): Date? {
    val long = readLong()
    return if (long != -1L) Date(long) else null
}
````

<br>

### **리스트 전달받기**

이제 리스트를 전달받아보자.  
`getSerializableExtra()` 메소드를 이용한다.  
전달받을 때에는 다음과 같이 코드를 작성한다.

````
photoList = intent.getSerializableExtra("photo_list") as ArrayList<PhotoData>
````

## 결론

이렇듯 액티비티로 데이터를 전달할 때, ArrayList형식도 별도의 구현을 통해 전달 가능하다.

<br>

## 향후과제

<br>

## 참고자료

<https://codechacha.com/ko/android-parcelable/>

<br>

*Writer: Jae-Hwan Lee*
