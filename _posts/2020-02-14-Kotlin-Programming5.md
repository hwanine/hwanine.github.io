---
toc: true
toc_sticky: true
categories:
  - Kotlin
title: IntelliJ에서의 코틀린 프로그래밍 - 프로퍼티 (2)
tags: [Kotlin, 코틀린, JAVA, Android, IntelliJ]
excerpt_separator: <!--more-->
---

# IntelliJ에서의 코틀린 프로그래밍 - 프로퍼티 (2)
<!--more-->
* **연구주제** : 코틀린 프로그래밍
* **연구목적** : 안드로이드에서의 코틀린 활용
* **연구일시** : 2020년 02월 14일 09:00~17:00
* **연구자** : 이재환 <ljh951103@naver.com>
* **연구장비** : HP EliteDesk 800 G4 TWR, Kotlin, Android studio, IntelliJ
* **관련연구** : Java, Android, Kotlin, IntelliJ

<br>
   
## 서론

이번 시간에도 코틀린에서의 프로퍼티에 대해 남은 내용에 대해 알아보도록 하겠다.

<br>
   
## 본론

### **정적 변수와 컴패니언 객체**

정적 변수를 사용하기위해 자바에서는 static 키워드를 사용했는데 코틀린에서는 이 키워드가 존재하지않는다.  
코틀린은 `컴패니언 객체`를 정적변수의 대체제로 사용한다.

````
class Person {
    var name: String = "JaeHwan"
    companion object {
        var language: String = "Korean"
        fun work( ) {
            println("Working")
        }
    }
}

fun main( ) {
    println(Person.language)
    Person.language = "English"
    Person.work( )
}
````

위의 예를 보면 companion 객체의 범위 안에 있는 코드들은 다음과 같이 객체 생성없이도 접근할 수 있고, 값을 변경할 수도 있다.

- 코틀린에서 자바 static 멤버와 연동하기

코틀린와 자바를 함께 사용하는 경우가 많다. 그렇다면 자바의 static 멤버 변수에 어떻게 접근할 수 있을까?

````
public class Customer {}
.. Java code ..
}
````
````
fun main( ) {
    println(Customer.LEVEL)
    Customer.login
}
````

이런식으로 자바의 스태틱 메소드나 변수에도 손쉽게 접근할 수 있다.  

그럼 자바에서도 코틀린 객체를 사용할 수 있을까?

````
class KCustomer {
    companion object {
        const val LEVEL = " "
        @JVMStatic fun login( ) = println("Login...")
    }
}
````
````
...
System.out.println(KCustomer.LEVEL);
KCustomer.login( );   @JvmStatic 사용했을 때
KCustomer.Companion.login( );   // @JvmStatic 사용하지 않았을 떄
...
````

- const는 컴파일 시간의 상수다. 자바에서 접근하기 위해 필요하다.
- @JVMStatic 애노티에션은 자바에서 코드를 해석할 때 Companion을 생략할 수 있게 해준다.

마찬가지로 자바에서 프로퍼티를 사용하고자 할 경우네는 @JvmField 애노테이션을 사용할 수 있다.

<br>

- 최상위 함수 사용

````
@file:JvmName("PKLevel")
fun packageLevelFunc( ){
    println("Package-Level Function")
}

fun main( ) {
    packageLevelFunc( )
}
````

위의 함수는 우리가 계속 써오던 방식이다. 이것은 객체없이 어떻게 실행되는 걸까?  
최상위함수인 packageLevelFunc( ) 함수는 역 커패일 해보았을 때 static으로 선언되어 있다.  
그리고 packageLevelFunctionKt 클래스가 자동으로 생성되어있음을 확인할 수 있다.

우리는 다음과 같은 코드로 이 클래스의 이름을 지정해줄 수 있다.

````
@file:JvmName("PKLevel")
fun packageLevelFunc( ){
    println("Package-Level Function")
}

fun main( ) {
    packageLevelFunc( )
}
````

역시나 내부적으로 static으로 선언되어 있기 때문에 main( ) 블록에서 객체없이 사용할 수 있고 자바에서도 정적 함수처럼 접근이 가능하다.

<br>

- object, 싱글톤

하위 클래스를 생성하지 않고 내용이 조금 변경된 클래스를 만들어야 할 때, 자바에서는 익명 내부 클래스를 만들어서 처리할 수 있다.  
코틀린에서는 그 기능을 object 표현식이나 선언으로 쉽게 처리할 수 있다.

````
// object 키워드를 사용한 방식
object OCustomer {
    var name = "Jaehwan"
    fun greeting ( ) = println("Hello world")
    val HOBBY = Hobby("Basketball")
}

// 컴패니언 객체를 사용한 방식
class CCusomer {
    const val HELLO = "hello"
    var name = "Jaehwan"
    @JvmField val HOBBY = Hobby("Football")
    @JvmStatic fun greeting( ) = println("Hello world")
}
````

다음 코드를 보면 object로 선언된 클래스는 객체 생성없이 이름의 점(.) 표기법으로 바로 사용할 수 있다. 싱글톤 패턴에 이용된다.  

이 방식을 사용하면 접근 시점에 객체가 생성된다. 따라서 생성자를 사용할 수 없지만 초기화 블록인 init은 들어갈 수 있다. 물론 클래스나 인터페이스를 상속할 수는 있다.  
그렇다면 자바에서 object 선언으로의 접근은 어떻게 이루어질까?

````
OCustomer.INSTANCE.getName( );
````

다음과 같이 INSTANCE를 통해 멤버에 접글할 수 있다.

<br>

- object 표현식

object 표현식은 선언과 달리 이름이 없으며 싱글톤이 아니다.  
따라서 object 표현식이 사용될 때마다 새로운 인스턴스가 생성된다.  
결과적으로 이름이 없는 익명 내부 클래스의 형태를 이 표현법을 통해 만들 수 있다.

````
open class Superman( ){
    open fun fly( ) = println("Flying in the air.")
}

fun main( ) {
    val pretendedMan = object: Superman( ) {   // object 표현식으로 구현의 재정의
        override fun fly( ) = print("I can't fly!")
    }
    pretendedMan.work( )
    pretendedMan.talk( )
    pretendedMan.fly( )
}
````

다음과 같이 fly( ) 메소드를 오버라이딩하여 사용할 수 있다.

딱 한 번만 구현되는 인터페이스 구현 클래스를 정의하기가 부담으러운 경우 다음과 같이 사용할 수 있다.

````
interface Shape {
    fun onDraw( )   // 구현해야 할 메소드
}

val triangle = object: Shape {
    override fun onDraw( ) {   // 딱 한번 구현
        ...
    }
}
````

객체는 필요하지만 상위 인터페이스나 클래스가 없는 경우 다음과 같이 사용할 수 있다.

````
fun foo( ) {
    val adHoc = object {
        var x: Int = 0
        var y: Int = 0
    }
    print(adHoc.x + adHoc.y)
}
````

이런 익명 객체는 지역이나 private 정의 영역에서만 사용할 수 있다.

````
class C {
    private fun foo( ) = object {   // 반환자료형은 익명 객체 자료형
        val x: String = "x"
    }

    fun publicFoo( ) = object {   // 반환 자료형은 Any
        val x: String = "x"
    }

    fun bar( ) {
        val x1 = foo( ).x   // 정상동작
        val x2 = publicFoo( ).x // 오류
    }
}
````

자바의 익명 내부 클래스와 같이, object 표현식 안의 코드는 둘러쌓인 범위 외부의 변수에 접근할 수 있다.

<br>

## 결론

코틀린의 프로퍼티에 대해 마저 알아보았다. 정적변수를 표현함에 있어서 코틀린만의 장점을 지닌 컴패니언 객체나, object 선언 및 표기법이 있었다.

<br>

## 향후과제

다양한 클래스와 인터페이스

<br>

## 참고자료

Do it! 코틀린 프로그래밍 (이지스퍼블리싱)  
<https://book.naver.com/bookdb/book_detail.nhn?bid=15000572>

<br>

*Writer: Jae-Hwan Lee*