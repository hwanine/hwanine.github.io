---
toc: true
toc_sticky: true
categories:
  - Kotlin
title: IntelliJ에서의 코틀린 프로그래밍 - 프로퍼티 (1)
tags: [Kotlin, 코틀린, JAVA, Android, IntelliJ]
excerpt_separator: <!--more-->
---

# IntelliJ에서의 코틀린 프로그래밍 - 프로퍼티 (1)
<!--more-->
* **연구주제** : 코틀린 프로그래밍
* **연구목적** : 안드로이드에서의 코틀린 활용
* **연구일시** : 2020년 02월 13일 09:00~17:00
* **연구자** : 이재환 <ljh951103@naver.com>
* **연구장비** : HP EliteDesk 800 G4 TWR, Kotlin, Android studio, IntelliJ
* **관련연구** : Java, Android, Kotlin, IntelliJ

<br>
   
## 서론

이번 시간에는 코틀린에서의 프로퍼티에 대해 알아보도록 하겠다.

<br>
   
## 본론

### **프로퍼티 접근**

프로퍼티는 자바에서의 필드, 즉 변수라고 생각할 수 있다.  
하지만 자바의 필드와 코틀린의 프로퍼티와는 중요한 차이가 있다.

- 자바의 필드: 접근 메소드를 일일이 만들어야한다.  
- 코틀린의 프로퍼티: 기본적인 접근 메소드를 모두 가지고 있기 때문에 프로퍼티라고 부르는 것이다.

자바를 많이 사용해본 사람들은 다음과 같은 코드를 많이 접해봤을 것이다.

````kotlin
...
public String getName( ){
    return name;
}

public String setName( ){
    this.name = name;
}
...
````

하지만 코틀린에서는 단 한줄로 모든 코드를 축약할 수 있다.

````kotlin
class Person(var name: String, var age: Int)
````

동작을 확인하기에 앞서 클래스를 만들자.

````kotlin
Class User(_id Int, _name: String, _age: Int) {
    val id: Int = _id
    var name: String = _name
    var age: Int = _age
}
````

다음과 같이 간소화할 수 있다.

````kotlin
class User(val id: Int, var name: String, var age: Int)
````

이제 동작을 확인해보자.

````kotlin
fun main( ){
    val user = User(1, "Lee", 26)
    val name = user.name   // getter에 의한 값 획득
    user.age = 27   // setter에 의한 값 지정
}

값을 제대로 얻어오고 지정할 수 있다.
````

- 게터와 세터를 지정할 수도 있다.

````kotlin
var age: Int = _age
    get( ) = field
    set(value) {
        field = value
    }
````
다음과 같이 게이터 세터를 지정할 수 있다.  
field는 이름이 정해져있어서 변경할 수 없지만, value는 정해진 이름이 아니므로 다른 이름으로 변경하여 사용할 수 있다.

여기서 field는 보조필드로서 각 프로퍼티의 값을 읽는 특별한 식별자이다.  
하지만 직접적으로 프로퍼티의 이름을 사용해서는 안된다. 무한 재귀 호출에 빠져 스택 오버플로우가 발생하게 되니깐 말이다.


위와 같은 지정 게터와 세터를 이용하여 커스텀할 수 있다.  
예를 들어, 입력문자를 대문자로 바꾸는 등의 특정 연산의 경우 유용하다.

````kotlin
set)(value){
    println("Changed")
    field = value.toUpperCase()
}
````

- 만일 field를 사용하지 않을 경우, 임시로 프로퍼티를 생성하여 field 대신 사용할 수 있다.

````kotlin
private var temp: String? = null
get() = temp
````

- 프로퍼티의 오버라이딩도 가능하다.

기본적으로 final 형태로 선언되기때문에 오버라이딩하게하려면 open 키워드를 사용하면 된다.

### **지연 초기화**

앞에서 init을 사용하여 초기화하는 방버을 설명했다. 지금은 지연 초기화에 대한 설명을 다루겠다.

1. lateinit

lateinit을 사용하여 지연 초기화가 가능하다. 하지만 몇 가지 제한사항이 있다.

- var로 선언된 프로퍼티만 가능
- 프로퍼티에 대한 게터와 세터를 사용할 수 없음.

````kotlin
class Person {
    lateinit var name: String
}
````

객체도 지연 초기화를 할 수 있다.  
생성자를 호출하는 시점에 초기화가 된다.

2. lazy

lazy를 이용한 지연 초기화도 가능하다. lazy는 val을 사용하는 읽기 전용의 프로퍼티를 지연 초기화할 때 매우 유용하다.

- 호출 시점에 by lazy {..}에 의해 블록 부분의 초기화를 진행한다.
- val 에서만 가능하다.
- 값을 변경할 수 없다.

````kotlin
val subject by lazy {
    println("lazy initialized")
    "kotlin"   // lazy 반환값
}
````

- by를 이용한 위임

by를 사용하면 하나의 클래스가 다른 클래스에 위임하도록 선언하여 위임된 클래스가 가지는 멤버를 참조 없이 호출할 수 있게 된다.

````kotlin
interface Car {
    fun go( ): String
}

class VanImpl(val power: String): Car {
    oveerride fun go( ) = ".."
}

class SportImpl(val power: String): Car {
    oveerride fun go( ) = ".."
}

class CarModel(val model: String, impl: Car): Car by impl {   // Car에 정의된 impl의 모든 멤버를 CarModel에 위임 
    fun carInfo(){
        printfln("..")
    }
}
````

이렇듯 프로퍼티, 자료형, 클래스 등을 위임할 수 있고, observable() 함수, vetoable() 함수도 위임할 수 있다.

- observable(): 프로퍼티 값이 변경되는지 감시하는 함수
    - by Delegates.observation()
- vetoable(): 조건에 따라 값 할당을 관리하는 함수
    - by Delegates.vetoable()

두 함수모두 Delegates 라이브러리를 사용한다. 자세한 코드는 다루지 않겠다.

<br>

## 결론

코틀린의 프로퍼티에 대해 알아보았다. 자바의 필드와 비교해서 '프로퍼티'라는 명칭에서 오는 게터와 세터를 따로 구현안해도 된다는 커다란 장점이 있었다.

<br>

## 향후과제

코틀린 프로퍼티 나머지 분량 설명

<br>

## 참고자료

Do it! 코틀린 프로그래밍 (이지스퍼블리싱)  
<https://book.naver.com/bookdb/book_detail.nhn?bid=15000572>

<br>

*Writer: Jae-Hwan Lee*