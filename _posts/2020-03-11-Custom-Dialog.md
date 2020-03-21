---
toc: true
toc_sticky: true
categories:
  - Android
title: Android - 연도와 월만 선택하는 Custom Datepicker Dialog 생성 및 활용 (Kotlin)
tags: [Kotlin, 코틀린, JAVA, Android, Dialog]
excerpt_separator: <!--more-->
---

# Android - 연도와 월만 선택하는 Custom Datepicker Dialog 생성 및 활용 (Kotlin)
<!--more-->
* **연구주제** : Android - 연도와 월만 선택하는 Custom Datepicker Dialog 생성 및 활용 (Kotlin)
* **연구목적** : 안드로이드에서의 코틀린 활용
* **연구일시** : 2020년 03월 11일 09:00~17:00
* **연구자** : 이재환 <ljh951103@naver.com>
* **연구장비** : HP EliteDesk 800 G4 TWR, Kotlin, Android studio, IntelliJ
* **관련연구** : Java, Android, Kotlin, Dialog

<br>
   
## 서론

개발중인 프로그램에서 연도와 달을 입력받아 검색하는 시스템이 있다.  
기본 DatePicker은 년, 월, 일을 다 받기때문에 쓰기에 적합하지 않다.  
따라서 우리는 연도와 월만 선택하는 Custom Datepicker Dialog를 생성해보고자 한다.  

<br>
   
## 본론

저번 포스팅에서 다이얼로그에 대해 알아보았다.  
DatePicker도 다이얼로그의 일종이기 때문에 원리는 똑같다.  
다만 그 다이얼로그를 DatePicker처럼 만들어주어야 한다.

````Kotlin
class YearMonthPickerDialog<Button : View?>(v: View, tag: String): DialogFragment() {
    private var listener: DatePickerDialog.OnDateSetListener? = null
    private final val MAX_YEAR = 2099
    private final val MIN_YEAR = 1980
    val v = v
    val tagname: String = tag
    var cal = Calendar.getInstance()
    fun setListener(listener: DatePickerDialog.OnDateSetListener?) {
        this.listener = listener
    }

    var btnConfirm: Button? = null
    var btnCancel: Button? = null
    @RequiresApi(Build.VERSION_CODES.N)
    override fun onCreateDialog(savedInstanceState: Bundle?): Dialog {
        val builder =
            AlertDialog.Builder(activity)
        val inflater = activity!!.layoutInflater
        val dialog: View = inflater.inflate(R.layout.year_month_picker, null).also {
            btnConfirm = it.findViewById<Button>(R.id.btn_confirm)
            btnCancel = it.findViewById<Button>(R.id.btn_cancel)
        }

        val monthPicker =
            dialog.findViewById<View>(R.id.picker_month) as NumberPicker
        val yearPicker =
            dialog.findViewById<View>(R.id.picker_year) as NumberPicker
        btnConfirm?.setOnClickListener(View.OnClickListener {

            if(tagname == "calendar") {
                val y = DateFragment.calDate.get(Calendar.YEAR)
                val m = DateFragment.calDate.get(Calendar.MONTH)
                DateFragment.calDate.add(
                    Calendar.MONTH,
                    12 * (yearPicker.value.toInt() - y) + (monthPicker.value.toInt()) - m
                )
                val month_left_button = v.findViewById<AppCompatImageButton>(R.id.cal_month_left)
                month_left_button?.performClick()
            }
            else if(tagname == "search")
            {
                v.searchview.setQuery("${yearPicker.value}" + " " + "${monthPicker.value}", true)
            }
            dismiss()

        })
        btnCancel?.setOnClickListener(View.OnClickListener {
            dismiss()
        })

        monthPicker.minValue = 1
        monthPicker.maxValue = 12
        monthPicker.value = cal[Calendar.MONTH] + 1
        val year = cal[Calendar.YEAR]
        yearPicker.minValue = MIN_YEAR
        yearPicker.maxValue = MAX_YEAR
        yearPicker.value = year
        builder.setView(dialog)

        return builder.create()
    }

}
````

다음과 같이 구성할 수 있다. 사실 버튼 클릭 리스너 부분은 참고하지 않아도 된다. 사용자 입맛에 맞게 설정할 수 있다.  
버튼은 레이아웃에 정의하여 사용할 수도 있고, AlertDialog에서 제공하는 PositiveButton, NegativeButton를 사용할 수도 있다.

<br>

````Kotlin
fun dialogCreate(view: View) {
        val pd: YearMonthPickerDialog<View> = YearMonthPickerDialog(view, "search")
        pd.show(supportFragmentManager, "YearMonthPickerTest")
    }
````

다이얼로그는 다음과 같이 생성되도록 해두었다.

<br>

## 결론

![image](https://user-images.githubusercontent.com/57826388/76139574-976ab300-6094-11ea-9e83-42e88fb49973.png)

이렇게 커스텀으로 원하는 다이얼로그를 개발자가 직접 만들 수 있다.  
처음엔 불편하다는 생각이 들지만 의외로 만들어보면 간단하고 커스터마이징 자유도도 높아서 좋다.

<br>

## 향후과제

<br>

## 참고자료

<https://dreamaz.tistory.com/199>  

<br>

*Writer: Jae-Hwan Lee*