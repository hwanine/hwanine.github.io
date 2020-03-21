---
toc: true
toc_sticky: true
categories:
  - Kotlin
title: IntelliJ에서의 코틀린 프로그래밍 - 흐름제어, 클래스, 객체 (1)
tags: [Kotlin, 코틀린, JAVA, Android, IntelliJ]
excerpt_separator: <!--more-->
---

# IntelliJ에서의 코틀린 프로그래밍 - 흐름제어, 클래스, 객체 (1)
<!--more-->
* **연구주제** : 코틀린 프로그래밍
* **연구목적** : 안드로이드에서의 코틀린 활용
* **연구일시** : 2020년 02월 11일 09:00~17:00
* **연구자** : 이재환 <ljh951103@naver.com>
* **연구장비** : HP EliteDesk 800 G4 TWR, Kotlin, Android studio, IntelliJ
* **관련연구** : Java, Android, Kotlin, IntelliJ

<br>
   
## 서론

이번 시간에는 코틀린에서의 흐름제어, 클래스와 객체를 알아보도록 하겠다.

<br>
   
## 본론

### **조건문**

코틀린의 if ~ else 문은 다른 프로그래밍 언어들과 큰 차이가 없다.  
하지만 다음과 같이 코딩하면 'return'을 굳이 지정해주지 않아도 된다.

````kotlin
fun main(){
    val a = 12
    val b = 7

    val max = if(a>b){
        println("a선택") 
        a // a가 반환되어 max에 할당
    }
    else {
        println("b 선택")
        b // b가 반환되어 max에 할당
    }
    println(max)
}
````
- in 연산자를 이용하여 범위를 조건에 포함시킬 수도 있다.

```kotlin
...
    if (score >=90) {
        grade = 'A'
    } else if (score in 80.0..89.9) {   // 범위가 80.0 ~ 89.9 에 포함되는 값
        grade = 'B'
    } else {
        grade = 'C'
    }
...
```

<br>

- When문은 다양한 조건을 처리할 수 있다. C언어나 Java에서의 case문과 유사하다.  
- 여러개의 조건을 하나의 조건으로 나열시킬 수 있다.
- 다음과 같이 when문 내부에 else문을 넣을 수도 있다.
- 함수의 반환값을 사용하여 비교 후 조건을 줄 수도 있다.

````kotlin
when(x) {
    1 -> print("x == 1")
    2 -> print("x == 2")
    3,4,5 -> PRINTF("X == 3,4,5")
    parseInt(s) -> print("함수값과 일치")
    else -> {
        print("x는 1~5 이외의 수)
    }
}
````

이밖에도 in을 사용할 수도 있고 인지가 없이 사용할 수도 있다.  
물론 다양한 자료형의 인자 또한 가능하다.

````kotlin
when {
    score >= 90.0 -> print("A")
    score in 80...89.9 -> print("B")
    score !in 80..100 -> print("F")    
}
````

<br>

### **반복문**

코틀린에서의 반복문으로 for문을 사용할 수 있는데 자바나 C언어의 형식과는 조금 다르다.

- for (요소 변수 in 컬렉션 or 범위) {본문}

````kotlin
for(x in 1..5){
    println(x)
}
````

다음과 같이 실행하면 1부터 5까지의 변수가 할당되어 출력된다.  
여기서 상행 반복외에도 하행반복 또한 존재한다.  
'downTo'를 사용한다. 또한 예를 들어 2단계씩 증가하고 싶다 하면 'step'을 사용할 수 있다.

````kotlin
for (i in 5 downTo 1) 
    print(i)
````
````kotlin
for (i in 1..5 step 2) 
    print(i)
````

다음과 같이 두 키워드를 혼합하여 사용할 수도 있다.

````kotlin
for(i in 5 downTo 1 step 2)
    print(i)
````

다음의 코드는 5에서 1 사이의 값에서 2씩 감소하는 값만 반복한다.  

while문과 do while문은 다른 언어와 같다.

<br>

### **클래스**

코틀린에서 클래스를 작성하는 방법도 자바와 크게 다르지 않다.  
생성자에 대해 얘기해볼건데, 코틀린에서 생성자는 주 생성자와 부 생성자로 구성된다.  
우선 부 생성자부터 알아보자.

````kotlin
Class Bird{
    var name: String
    var color: String

    constructor(name: String, color: String){
        this.name = name
        this.color = color
    }
}

fun main(){
    val coco = Bird("mybird", "yellow")
}
````

다음과 같이 constructor 내부에 부 생성자를 만들 수 있다.  
부 생성자를 여러 개 사용할 때에는 매개변수를 다르게 정의하면 된다.  
여기서 this 키워드를 사용하지 않고 표현할 수도 있다. 바로 '_'를 매개변수 이름 앞에 붙이는 것이다.

````kotlin
Class Bird{
    var name: String
    var color: String

    constructor(_name: String, color: Int){
        name = _name
        color = color
    }
}

fun main(){
    val coco = Bird("mybird", "yellow")
}
````

<br>

그렇다면 주 생성자는 어디있을까?

````kotlin
Class Bird constructor(_name: String, color: Int){
    var name: String = _name
    var color: String = color

}

fun main(){
    val coco = Bird("mybird", "yellow")
}
````

주 생성자는 다음과 같이 클래스의 이름과 함께 정의할 수 있는 기법이다.  
사실 주 생성자의 경우 특별한 경우가 아니라면 일반적으로 constructor를 생략하여 사용할 수 있다.  

또한 프로퍼티를 포함하여 주 생성자를 생성하는 것도 가능하다.

````kotlin
Class Bird constructor(var name: String, var color: Int){

}

fun main(){
    val coco = Bird("mybird", "yellow")
}
````

- 객체를 생성할 때, 별도로 초기화 블록을 지정할 수도 있다. 키워드 'init'을 활용하면 된다.

````kotlin
class Brld...{
    init{
        초기화 블록
    }
}
````

- 프로퍼티의 기본값을 지정할 수도 있다.

````kotlin
class Bird(var name: String = "NoName, var color: String){
    ...
}
fun main(){
    val coco = Bird(color = "yellow") // 기본값이 없는것만 전달가능하다.
}
````

<br>

## 결론

조건분기와 클래스에 대해 알아보았다. 코틀린과 자바의 차이점에 중점을 맞추어 학습을 진행하였기 때문에 자바와 중복되는 내용은 빠진 내용이 많을 것이다.

<br>

## 향후과제

코틀린의 클래스와 객체에 대한 학습

<br>

## 참고자료

Do it! 코틀린 프로그래밍 (이지스퍼블리싱)  
<https://book.naver.com/bookdb/book_detail.nhn?bid=15000572>

<br>

*Writer: Jae-Hwan Lee*