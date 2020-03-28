---
toc: true
toc_sticky: true
categories:
  - Android
title: Android - 커스텀 다이얼로그를 활용한 유사 이미지 검색 로직 및 소스코드 (3) (Kotlin)
tags: [Kotlin, 코틀린, JAVA, Android, Dialog, RecyclerView]
excerpt: "커스텀 다이얼로그 객체들의 클릭이벤트에 따라 또다른 커스텀 다이얼로그를 생성해 보겠습니다."
---

* **연구주제** : Android - 커스텀 다이얼로그를 활용한 유사 이미지 검색 로직 및 소스코드 (3) (Kotlin)
* **연구목적** : 안드로이드에서의 코틀린 활용
* **연구일시** : 2020년 04월 20일 09:00~17:00
* **연구자** : 이재환 <ljh951103@naver.com>
* **연구장비** : HP EliteDesk 800 G4 TWR, Kotlin, Android studio, IntelliJ
* **관련연구** : Java, Android, Kotlin, Dialog, RecyclerView

<br>
   
## 서론

지난 포스팅 까지 리사이클러 뷰를 담고있는 커스텀 다이얼로그를 만들고 모든 추가적인 다이얼로그를 생성해서 화면을 구성해보았습니다.  
이번 포스팅에서는 내부 로직을 구현하고 소스코드 공개와 리뷰를 해보겠습니다.

<br>
   
## 본론

### **리사이클러뷰 다이얼로그 구성**

처음 다이얼로그를 호출하면 다음의 다이얼로그 생성 함수가 실행됩니다.  
유사 이미지를 검색해야하는데, 위치 정보가 일치하고 시간이 1분 이상 차이가 나지않는 이미지를 유사 이미지로 선정합니다.
이 때, DB에서 비동기로 데이터들을 호출해줍니다.

처음 해당 이미지와 위치정보가 같은 사진 id들을 출력합니다.  
그 후, 그 id들로 이미지들의 날짜정보를 확인하여 최종적으로 사진 id와 경로를 출력합니다.

````kotlin
override fun onCreateDialog(savedInstanceState: Bundle?): Dialog {
        stringToCalendar()
        recyclerView = v.findViewById<RecyclerView>(R.id.similar_RecycleView)
        setView(ArrayList())
        DBThread.execute {
            getOpenDirByIdCursorDESC(vm, vm.getOpenLocationDirIdCursor(location))
        }

        val maindlgBuilder: androidx.appcompat.app.AlertDialog.Builder = androidx.appcompat.app.AlertDialog.Builder(    // 메인 다이얼로그
            context!!, android.R.style.Theme_DeviceDefault_Light_Dialog_NoActionBar_MinWidth)
        maindlgBuilder.setView(v)

        val dlg = maindlgBuilder.create()
        saveSimilarPhoto(dlg)
        return dlg
    }

    private fun getOpenDirByIdCursorDESC(vm : PhotoViewModel, idCursor : Cursor?) {
        if (vm.CursorIsValid(idCursor)) {
            idCursor!!.moveToLast()
            do {
                try {
                    val data = vm.getThumbnailDataByIdCursor(context!!, idCursor!!, calendar)
                    if (data != null) {
                        recyclerAdapter.addThumbnailList(data)
                        similarList.add(data)
                        MainHandler.post { recyclerAdapter.notifyItemInserted(recyclerAdapter.getSize()) }
                    }
                } catch (e: Exception) { }
            } while (idCursor!!.moveToPrevious())
            idCursor.close()
        }
    }
````

<br>

### **다이얼로그 이미지 구성 및 추가 다이얼로그**

DB에서 출력된 이미지들은 다음과 같은 함수를 거쳐 어댑터에서의 작업과정을 통해 이미지가 출력됩니다.  
또한 이미지들의 각각에 클릭 리스너도 달아주었기 때문에 여기서는 클릭에 대한 행동만 정의해줍니다.  

클릭했을 때, 추가 다이얼로그를 생성하게 됩니다. 이 다이얼로그에도 해당 이미지를 생성해줍니다.  
그리고 확인 버튼을 눌렀을 때, DB에서 가져온 이미지 리스트에서 해당 이미지를 빼고 화면을 업데이트 시켜줍니다.

````kotlin
private fun setView(list: ArrayList<thumbnailData>) {
        recyclerAdapter =
            RecyclerAdapterPhoto(activity, list) { thumbnailData, num ->
                val similarImageSelectView: View = layoutInflater.inflate(R.layout.similar_image_select, null)
                ImageLoder.execute(ImageLoad(context!!, similarImageSelectView.select_photo, thumbnailData.photo_id))
                val dlgBuilder: androidx.appcompat.app.AlertDialog.Builder = androidx.appcompat.app.AlertDialog.Builder(    // 확인 다이얼로그
                    context!!,  android.R.style.Theme_DeviceDefault_Light_Dialog_NoActionBar_MinWidth)
                dlgBuilder.setCancelable(false)

                dlgBuilder.setView(similarImageSelectView)
                dlgBuilder.setTitle("보존할 사진이 맞나요?")
                dlgBuilder.setIcon(R.drawable.ic_image)
                val dlgselect = dlgBuilder.create()
                dlgselect.show()
                similarImageSelectView.select_cancel.setOnClickListener{
                    dlgselect.cancel()
                }
                similarImageSelectView.select_ok.setOnClickListener{
                    similarList.removeAt(num)
                    setView(similarList)
                    setPhotoSize(2, 5)
                    selectnum++
                    dlgselect.cancel()
                    Toast.makeText(context!!, "입력 완료 되었습니다. \n저장을 누르시면 입력된 사진들만 저장됩니다.", Toast.LENGTH_SHORT).show()
                }

            }
        recyclerView.adapter = recyclerAdapter
        val lm = GridLayoutManager(context, 2)
        recyclerView.layoutManager = lm
    }
````

### **이미지 사이즈 설정**

<br>

이미지의 사이즈 또한 정해줘서 어댑터로 보내, 업데이트 합니다.

````kotlin
private fun setPhotoSize(row : Int, padding : Int) {
        val display = activity!!.windowManager.defaultDisplay
        val deviceSize = Point()
        display.getSize(deviceSize)
        val width = deviceSize.x * 88/100
        val size = width!! / row - 2*padding
        recyclerAdapter.setPhotoSize(size, padding)
    }
````

<br>

### **알림 다이얼로그 및 이미지 최종 삭제**

다음은 다이얼로그에서 저장 버튼을 누를 시, 호출되는 알림 다이얼로그를 구성하며 이미지들을 최종 저장하는 부분입니다.  
이미지들 읽어왔을 때, similarList에 있는 이미지들을 모두 삭제시켜줍니다.  
이미지를 지웠을 경우, 전에 있던 뷰에서 이미지가 보여지면 오류를 발생시키기 때문에 이전 액티비티 또한 종료시켜줍니다.

````kotlin
private fun saveSimilarPhoto(dlg: androidx.appcompat.app.AlertDialog) {
        v.similar_cancel.setOnClickListener {
            dlg.cancel()
        }
        v.similar_ok.setOnClickListener {
            val warningBuilder: androidx.appcompat.app.AlertDialog.Builder = androidx.appcompat.app.AlertDialog.Builder(context!!,    // 경고 다이얼로그
                android.R.style.Theme_DeviceDefault_Light_Dialog_NoActionBar_MinWidth)
            warningBuilder.setTitle("알림") //제목
            warningBuilder.setMessage("선택한 사진들을 제외하고는 모든 사진이 삭제됩니다.\n정말 저장하시겠습니까?\n\n (선택한사진: ${selectnum} 개)") // 메시지
            warningBuilder.setCancelable(false)
            warningBuilder.setPositiveButton("확인", DialogInterface.OnClickListener { dialog, which ->
                for(i in similarList.indices) {
                    DBThread.execute { vm.Delete(context!!, similarList[i].photo_id) }
                }
                dialog.cancel()
                dlg.cancel()
                val pager: Activity = context!! as Activity        // 액티비티 종료
                pager.finish()
                Toast.makeText(context!!, "${selectnum} 개의 사진이 저장 완료 되었습니다. \n", Toast.LENGTH_SHORT).show()
            })
            warningBuilder.setNegativeButton("취소", DialogInterface.OnClickListener { dialog, which ->
                dialog.cancel()
            })
            val dlgWarning = warningBuilder.create()
            dlgWarning.show()
        }

    }
````

<br>

### **다이얼로그 호출**

필요한 파라미터를 넣고 다이얼로그를 생성합니다.

````kotlin
private fun similarImage() {
        val similarImageDialogView: View = layoutInflater.inflate(R.layout.similar_image_layout, null)
        val dlg = similarImageDialog(similarImageDialogView, vm, location_name.text.toString(), date_name.text.toString())
        dlg.setCancelable(false)
        dlg.show(supportFragmentManager, "similarImageDialog")
    }
````

<br>

### **전체 소스코드**

````kotlin
package com.example.wimmy.Activity

import android.annotation.SuppressLint
import android.app.Activity
import android.content.Context
import android.content.Intent
import android.graphics.Bitmap
import android.graphics.BitmapFactory
import android.net.Uri
import android.os.Build
import android.os.Bundle
import android.provider.MediaStore
import android.view.LayoutInflater
import android.view.MenuItem
import android.view.View
import android.widget.ImageView
import android.widget.Toast
import androidx.annotation.RequiresApi
import androidx.appcompat.app.AlertDialog
import androidx.appcompat.app.AppCompatActivity
import androidx.appcompat.widget.AppCompatTextView
import androidx.lifecycle.ViewModelProviders
import androidx.viewpager.widget.ViewPager
import com.example.wimmy.*
import com.example.wimmy.Adapter.PagerRecyclerAdapter
import com.example.wimmy.Activity.Main_PhotoView.Companion.list
import com.example.wimmy.db.MediaStore_Dao
import com.example.wimmy.db.PhotoViewModel
import com.example.wimmy.dialog.similarImageDialog
import com.example.wimmy.dialog.tagInsertDialog
import com.google.android.material.bottomnavigation.BottomNavigationView
import kotlinx.android.synthetic.main.photoview_frame.*
import java.io.ByteArrayOutputStream

class PhotoViewPager : AppCompatActivity(), BottomNavigationView.OnNavigationItemSelectedListener  {
    private var recyclerAdapter : PagerRecyclerAdapter?= null
    private lateinit var viewPager: ViewPager
    private lateinit var vm : PhotoViewModel
    private lateinit var text_name : AppCompatTextView
    private lateinit var favorite: ImageView
    private lateinit var tag_name : AppCompatTextView
    private lateinit var date_name : AppCompatTextView
    private lateinit var location_name : AppCompatTextView
    private lateinit var Inflater: LayoutInflater

    private var index  = 0
    private var delete_check: Int = 0



    @RequiresApi(Build.VERSION_CODES.LOLLIPOP)
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        setContentView(R.layout.photoview_frame)
        val view: View = findViewById(R.id.imgViewPager)
        vm = ViewModelProviders.of(this).get(PhotoViewModel::class.java)

        getExtra()
        text_name = findViewById<AppCompatTextView>(R.id.imgView_text)
        date_name = findViewById<AppCompatTextView>(R.id.imgView_date)
        location_name = findViewById<AppCompatTextView>(R.id.imgView_location)
        tag_name = findViewById<AppCompatTextView>(R.id.imgView_tag)
        favorite = findViewById<ImageView>(R.id.favorite)

        bottom_photo_menu.setOnNavigationItemSelectedListener(this)
        try {
            setView(view, mainphoto_toolbar, bottom_photo_menu)
        } catch (e: Exception){
            Toast.makeText(this, "위치 데이터 초기 설정중입니다. 잠시만 기다려주세요", Toast.LENGTH_SHORT)
                .show()
        }

        toolbar_text(index)
        Inflater = LayoutInflater.from(this)

        viewPager.addOnPageChangeListener(object : ViewPager.OnPageChangeListener {

            override fun onPageScrollStateChanged(state: Int) {

            }

            override fun onPageScrolled(position: Int, positionOffset: Float, positionOffsetPixels: Int) {
                mainphoto_toolbar!!.visibility = View.VISIBLE
                bottom_photo_menu.visibility = View.VISIBLE
            }

            override fun onPageSelected(position: Int) {
                index = position
                toolbar_text(position)
            }
        })
    }

    @RequiresApi(Build.VERSION_CODES.LOLLIPOP)
    private fun setView(view: View, toolbar: androidx.appcompat.widget.Toolbar, bottombar: View) {
        viewPager = view.findViewById(R.id.imgViewPager)
        recyclerAdapter = PagerRecyclerAdapter( this, list, toolbar, bottombar )

        viewPager.adapter = recyclerAdapter
        viewPager.setCurrentItem(index, false)

    }

    override fun onBackPressed() {
        finishActivity()
    }

    @SuppressLint("SimpleDateFormat")
    fun toolbar_text(position: Int){
        val id = list[position].photo_id

        DBThread.execute {
            val data = vm.getName(this.applicationContext, id)
            MainHandler.post { text_name.text = data }
        }

        DBThread.execute {
            val data = vm.getStringDate(applicationContext, id)
            MainHandler.post { date_name.text = data}
        }

        DBThread.execute {
            val data = vm.getLocation(applicationContext, id)
            MainHandler.post { location_name.text = data}
        }

        DBThread.execute {
            val data = vm.getTags(id)
            MainHandler.post { tag_name.text = data }
        }

        DBThread.execute {
            val data = vm.getFavorite(id)
            if (data) favorite.setImageResource(R.drawable.ic_favorite_checked)
            else favorite.setImageResource(R.drawable.ic_favorite)
        }

        favorite.setOnClickListener {
            DBThread.execute {
                val data = vm.changeFavorite(id)
                if (data) favorite.setImageResource(R.drawable.ic_favorite_checked)
                else favorite.setImageResource(R.drawable.ic_favorite)
            }
        }
    }


    fun getExtra(){
        if (intent.hasExtra("index")) {
            index = intent.getIntExtra("index", 0)
        }
        else {
            Toast.makeText(this, "전달된 이름이 없습니다", Toast.LENGTH_SHORT).show()
        }
    }

    @RequiresApi(Build.VERSION_CODES.LOLLIPOP)
    override fun onNavigationItemSelected(p0: MenuItem): Boolean {

        when(p0.itemId){
            R.id.menu_tag_insert -> {
                insertTag()
            }
            R.id.menu_share -> {
                share()
            }
            R.id.menu_similar -> {
                similarImage()
            }
            R.id.menu_delete -> {
                delete(imgViewPager, mainphoto_toolbar, bottom_photo_menu)
            }
        }

        return true
    }

    private fun similarImage() {
        val similarImageDialogView: View = layoutInflater.inflate(R.layout.similar_image_layout, null)
        val dlg = similarImageDialog(similarImageDialogView, vm, location_name.text.toString(), date_name.text.toString())
        dlg.setCancelable(false)
        dlg.show(supportFragmentManager, "similarImageDialog")
    }

    private fun insertTag() {
        val popupInputDialogView: View = layoutInflater.inflate(R.layout.tag_diaglog, null)
        val dlg = tagInsertDialog(popupInputDialogView, vm, index, tag_name)
        dlg.setCancelable(false)
        dlg.show(supportFragmentManager, "tagInsertDialog")
    }


    private fun share() {
        val intent = Intent(Intent.ACTION_SEND)
        val path = MediaStore_Dao.getPathById(this, list[index].photo_id)
        var bitmap = BitmapFactory.decodeFile(path)
        bitmap =  MediaStore_Dao.modifyOrientaionById(this, list[index].photo_id, bitmap)
        val uri: Uri? = getImageUri(this, bitmap)
        intent.setType("image/*")
        intent.putExtra(Intent.EXTRA_STREAM, uri)
        val chooser = Intent.createChooser(intent, "친구에게 공유하기")
        startActivity(chooser)
    }


    private fun getImageUri(context: Context, inImage: Bitmap): Uri? {
        val bytes = ByteArrayOutputStream()
        inImage.compress(Bitmap.CompressFormat.JPEG, 100, bytes)
        val path: String = MediaStore.Images.Media.insertImage(
            context.contentResolver,
            inImage,
            "Title",
            null
        )
        return Uri.parse(path)
    }

    @RequiresApi(Build.VERSION_CODES.LOLLIPOP)
    private fun delete(view: View, toolbar: androidx.appcompat.widget.Toolbar, bottombar: View) {
        val dlg: AlertDialog.Builder = AlertDialog.Builder(this@PhotoViewPager,  android.R.style.Theme_DeviceDefault_Light_Dialog_NoActionBar_MinWidth)
        dlg.setTitle("사진 삭제")

        dlg.setMessage("정말 삭제하시겠습니까? ")
        dlg.setCancelable(false)
        dlg.setIcon(R.drawable.ic_delete)
        dlg.setPositiveButton("확인") { _, _ ->
            val id = list[index].photo_id
            DBThread.execute { vm.Delete(this, id) }

            list.removeAt(index)
            Toast.makeText(this, "삭제 완료 되었습니다.", Toast.LENGTH_SHORT).show()
            if(index == 0 && list.size == 0) {
                finishActivity()
            } else {
                if (index >= list.size) {
                    index -= 1
                }
                setView(view, toolbar, bottombar)
                toolbar_text(index)
            }
            delete_check = 1
        }
        dlg.setNegativeButton("취소") { _, _ -> }
        dlg.show()
    }

    private fun finishActivity() {
        val intent = Intent()
        intent.putExtra("index", index)
        setResult(Activity.RESULT_OK, intent)
        finish()
    }
}
````

<br>

## 결론

지금까지 리사이클러 뷰를 담고있는 커스텀 다이얼로그를 만들고 모든 추가적인 다이얼로그를 생성해서 화면을 구성해 보았고, 내부 로직들을 완성해보았습니다.    
교육용이나 예제가 아닌 제 프로젝트 코드 위주라서 그대로 쓰시기에는 어려우실 것 같아요.   
참고만 해주시는게 좋으실듯 합니다.

궁금한점 있으시면 언제든 댓글 남겨주세요.

<br>

## 향후과제

<br>

## 참고자료

<br>

*Writer: Jae-Hwan Lee*
