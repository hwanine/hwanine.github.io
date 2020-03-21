---
toc: true
toc_sticky: true
categories:
  - Kotlin
title: IntelliJ에서의 코틀린 프로그래밍 - 환경, 변수, 함수
tags: [Kotlin, 코틀린, JAVA, Android, IntelliJ]
excerpt_separator: <!--more-->
---

# IntelliJ에서의 코틀린 프로그래밍 - 환경, 변수, 함수 
<!--more-->
* **연구주제** : 코틀린 프로그래밍
* **연구목적** : 안드로이드에서의 코틀린 활용
* **연구일시** : 2020년 02월 10일 09:00~17:00
* **연구자** : 이재환 <ljh951103@naver.com>
* **연구장비** : HP EliteDesk 800 G4 TWR, Kotlin, Android studio, IntelliJ
* **관련연구** : Java, Android, Kotlin, IntelliJ

<br>
   
## 서론

### **Kotlin**

![image](https://user-images.githubusercontent.com/57826388/74135332-7b0f5e00-4c2f-11ea-9f3f-908d1f0a4695.png)

`코틀린`은 무엇인가? 이전 코틀린 포스팅에서 다룬적이 있다. 코틀린은 젯브레인즈에서 개발한 프로그래밍 언어이다.  
코틀린은 다음과 같은 프로그래밍이 가능한 멀티플랫폼 언어이다.

- Kotlin/JVM: 자바 가상 머신에서 동작하는 애플리케이션을 만들 수 있다.
- Kotlin/JS: 자바스크립트로 웹 브라우저에서 동작하는 애플리케이션을 만들 수 있다.
- Kotlin/Native: LLVM 컴파일러를 이용하여 여러 플랫폼을 타깃으로 하는 애플리케이션을 만들 수 있다.

또한 안드로이드 공식언어로 채택되어 기존의 자바에서 흐름에 따라 이제는 코틀린이 안드로이드를 플랫폼을 주도하게 된다는 전망이 매우 크다.


<br>

### **IntelliJ**

`IntelliJ IDEA`는 젯브레인즈에서 만든 Kotlin 개발도구이다. 우리는 이 IDE를 이용하여 코틀린을 실습할 것이다.  
설치과정 및 환경구성은 생략하고 바로 실습으로 들어가도록 하겠다. 

<br>

## 본론

필자가 다룰 포스팅에서는 자바와 겹치지않는 내용만을 다루겠다.

### **변수**

>변수 선언

- val: 변하지 못하는 final 변수 선언
- var: 변경 가능한 변수 선언

```` 
fun main(){
    val number: Int = 10
    val number2 = 20

    number = 30 // 에러
}
````

변수를 선언할 떄, 자료형을 값에 따라 알아서 추론해주기 때문에 다음과 같이 선언해도 된다.  
number = 30의 경우, 자료형이 'val'이기 때문에 에러를 출력하게된다. 

<br>

```` 
fun main(){
    var number: Int = 10
    number = 30
}
````

다음과 같이 var으로 선언하게되면 변할 수 있기 때문에 정상동작한다.  
코틀린의 자료형은 참조형 자료형을 사용한다. 참조형으로 선언한 후 성능최적화를 위해 코틀린 컴파일러에서 다시 기본형으로 대체된다.

```` 
fun main(){
    var number: Int = 10
    number = 30
}
````

````
fun main(){
    var number: Int = 10
    var str: String = "Hello"
    var ch: Char = 's'
}
````

<br>

변수를 출력하고자 할 때에는 '$' 기호를 변수앞에다 붙인다.  
참고로 코틀린은 변수에 null값을 허용하지 않는다.
````
fun main() {
    val num: Int = 15
    println("Hello and $num")
}

=> Hello and 15
````

- 이중 등호 (==): 값만 비교  
- 삼중 등호 (===): 참조 주소 비교

<br>

스마트 캐스트를 활용하여 자동으로 동적으로 자료형을 이용할 수 있다.

````
var test: Number = 12.2 // Float
var test2: Number = 15 // Int
var test3: Any = "hi" // Any의 경우 최상위 클래스로 어떤 자료형이든 가능
````

<br>

자료형 검사하기  

````
if(num is Int){
    print(num)
} else if (num !is Int){
    print("Not a Int")
}
````

자료형에 별명을 붙일 수도 있다.

````
typealias Username = String // String을 Username이라는 별명으로 대체
val user: Username = "JaeHwan" // Username은 String과 같은 표현으로 사용
````

<br>

### **함수**

함수의 활용

다음과 같이 다른 언어와 마찬가지로 활용한다.  
만약 반환값이 없는 경우 'Unit'을 명시하고 생략하면 디폴트값으로 알아서 추론하게 된다.
````
fun sum(a: Int, b: Int): Int {
    var sum = a + b
    return sum
}

fun main() {
    val result = sum(3, 2)
    println(result)
}
````

매개변수와 이름을 함께 호출할 수도 있다.

````
fun name(x: Int, y: Int): Int {
    println(x + y)
}

fun main() {
    name(x=200, y=100)
}
````

힘수형 프로그램은 프로그래밍 언어론 학습을 개인적으로 하는것으로 하고 여기서 넘어가곘다.

<br>

## 결론

오늘은 코틀린을 설치하고 기초 문법과 변수, 그리고 함수를 알아보았다.  
많은 코드를 다루지는 않았지만 일단 자바보다 훨씬 간결하다는 점에서 커다란 장점이 느껴진다.

<br>

## 향후과제

코틀린의 조건분기, 클래스와 객체에 대한 학습

<br>

## 참고자료

Do it! 코틀린 프로그래밍 (이지스퍼블리싱)  
<https://book.naver.com/bookdb/book_detail.nhn?bid=15000572>

<br>

*Writer: Jae-Hwan Lee*

