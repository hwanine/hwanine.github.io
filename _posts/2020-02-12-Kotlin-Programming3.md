---
toc: true
toc_sticky: true
categories:
  - Kotlin
title: IntelliJ에서의 코틀린 프로그래밍 - 흐름제어, 클래스, 객체 (2)
tags: [Kotlin, 코틀린, JAVA, Android, IntelliJ]
excerpt_separator: <!--more-->
---

# IntelliJ에서의 코틀린 프로그래밍 - 클래스, 객체 (2)
<!--more-->
* **연구주제** : 코틀린 프로그래밍
* **연구목적** : 안드로이드에서의 코틀린 활용
* **연구일시** : 2020년 02월 11일 09:00~17:00
* **연구자** : 이재환 <ljh951103@naver.com>
* **연구장비** : HP EliteDesk 800 G4 TWR, Kotlin, Android studio, IntelliJ
* **관련연구** : Java, Android, Kotlin, IntelliJ

<br>
   
## 서론

이번 시간에는 못다한 코틀린에서의 클래스와 객체를 마저 알아보도록 하겠다.

<br>
   
## 본론

코틀린에서도 어떤 클래스에서 하위 클래스를 생성할 수 있다. 상속의 개념을 말한다.  
그 표현 방식은 조금 다르다.

````kotlin
open class 기반 클래스 이름 {   // 묵시적으로 Any로 상속, open으로 파생 가능
    ...
}
class 파생 클래스 이름 : 기반 클래스 이름(){   // 기반 클래스로부터 상속됨
    ...
}
````

그럼 변수 언언과 클래스 상속을 구분할 수 있다.

````kotlin
val somVal: Int   // 변수 선언
open class BaseClass(someArgs: Int)   // 상속 가능한 클래스
class SomeClass(someArgs: Int) : BaseClass(someArgs)   // 클래스 상속 선언
class someClass : BaseClass { ..constructor( ).. }   // 부 생성자를 사용할 때, 상속 선언
````

다음 코드를 보면 변수 선언은 공백없이 콜론 기호(:)를 붙이지만, 클래스 선언의 경우 공백이 하나 있다는 사실을 알 수 있다.  
문법상 중요한 의미는 아니고 코딩 관례이다.

- 오버로딩 또한 매개변수를 다르게 정의하여 사용할 수 있다.  

- 오버라이딩의 경우에는 코틀린에서는 반드시 override 키워드를 면시하여 재정의됨을 알려야한다.
- 오버라이딩을 막고자 한다면 앞에 final 키워드 까지 사용해주면 된다.

````kotlin
oepn class Bird {
    fun fly( ) { ... }   // 최종 메서드로 오버라이딩 불가
    open fun sing( ) { ... }   // open이 있으므로 오버라이딩 가능
}

class Lark( ) : Bird( ) {
    fun fly( ) { ... }   // 에러, 상위 메서드에 open 키워드가 없기 때문ㅇ
    override fun sing ( ) { ... }   // 재정의됨
    final override fun sing ( ) { ... }   // 재정의, 하위클래스에서 재정의를 막음
}
````

이너 클래스를 선언하고 바깥 클래스를 호출할 수 있다.

````kotlin
open class Base {
    open val x: Int = 1
    open fun f( ) = println("Base Class")
}
open Child : Base( ) {
    override val x: Int = super.x + 1
    override fun f( ) = println("Child")

    inner class Inside {
        fun f( ) = println("Inside Class")
        fun test( ) {
            f( )
            Child( ).f( )
            super@Child.f( )
            printf("[Inside] ${super@Child}")
        }
    }
}

fun main( ) {
    val c1 = Child( )
    c1.Inside( ).test( )
}
````

다음 코드를 동작하면 어떻게 출력될까?  
먼저 이너 클래스의 f( )이 실행된다.  
그리고 바깥 클래스의 f( ),  
Base 클래스의 f( )에 접근한다.

이렇듯 바깥 클래스를 호출하는 방법은 super 키워드와 @기호, 바깥 클래스의 이름을 작성한다.

<br>
- 인터페이스에서도 참조할 수 있다.

````kotlin
open class A {
    open fun f( ) = println("A Class f( )")
    fun a( ) = println("A Class a( )")
}

interface B {
    fun f( ) = println("B Interface f( )")
    fun b( ) = println("B Interface b( )")
}

class C : A( ), B {
    override fun f( ) = println("C Class f( )")
    fun test( ) {
        f( )   // 현재 클래스의 f( )
        b( )   // 인터페이스 B의 b( )
        super<A>.f( )   // A 클래스의 f( )
        super<B>.f( )   // B 클래스의 f( )
    }
}

fun main( ) {
    val c = C( )
    c.test( )
}
````

다음을 출력하면 결과는 어떻게 나올까?

````
C Class f( )
B Interface b( )
A Class f( )
B Interface f( )
````

클래스는 1개만 상속 가능하고 인터페이스는 여러 개를 지정할 수 있다. 이 때, f( )이 중복되고 있는데 이 경우에는 

````kotlin
super<A>.f( )
super<B>.f( )
````
 
이렇게 구분할 수 있다. f( )를 그냥 사용할 경우 현재 클래스의 f( )를 호출한다.

우리는 어떠한 클래스를 작성할 때, 숨겨야 하는 기능이나 속성이 있을 수 있다.  
이 개념을 캡슐화라고 하고 객체 지향 프로그래밍에서 가장 큰 특징이 된다.

각 클래스나 메서드, 프로퍼티의 접근 범위(가시성)를 지정해줄 수 있다.

- private: 외부에서 접근 불가
    - 선언된 클래스 안의 멤버만 접근 가능
- public: 어디서든 접근가능(기본값)
- protected: 외부에서 접글할 수 없지만 하위 상속 요소에서는 가능
    - 상속된 하위 클래스에서는 접근할 수 있지만, 그 외에는 접근 불가
    - 최상위 클래스에는 protected를 사용할 수 없다.
- internal: 같은 정의의 모듈 내부에서는 접근 가능

가시성 지시사에 대해선 이미 자바나 다른 언어에서 학습해왔기 때문에 별도로 예제 코드로 다루지 않겠다.

<br>

## 결론

클래스와 객체에 대해 알아보았다. 전체적인 개념은 자바와 같고 표기하는 방법이 다르기 때문에 이해하고 실사용하는데 어려운 부분은 없을 것이다.

<br>

## 향후과제

코틀린의 프로퍼티

<br>

## 참고자료

Do it! 코틀린 프로그래밍 (이지스퍼블리싱)  
<https://book.naver.com/bookdb/book_detail.nhn?bid=15000572>

<br>

*Writer: Jae-Hwan Lee*