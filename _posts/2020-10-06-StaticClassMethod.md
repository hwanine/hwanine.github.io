---
toc: true
toc_sticky: true
categories:
  - C#
title: 정적 메서드 사용에 따른 클래스 사용 팁
tags: [C#, WPF, MVVM]
excerpt: "정적 메서드를 사용할 때 정적 클래스를 사용할까요? 일반 클래스를 사용할까요?"
---

# 정적 메서드, 정적 클래스

## Static 키워드

다들 아는 내용으로, Static 키워드를 주면 정적 클래스나 정적 메서드, 정적 인스턴스 변수를 선언할 수 있습니다.

정적 선언이 되면 이들은 별도의 기억 공간을 가집니다.

해당 클래스의 모든 객체들에 의해 공유될 수 있습니다.

또한 처음 실행될 때, 미리 값을 할당 받습니다.

<br>

## 클래스 내부 인스턴스 변수 유무

그런데 코딩중 한 가지 의문이 들었습니다.

정적 메서드를 사용하기위해 임의의 클래스를 만들었는데 이 클래스도 static 키워드를 달아서 정적 클래스를 달아주어야 할까요?

그렇지 않습니다. 클래스안에 static 메서드가 존재하면 class가 정적이든 아니든, 언제든지 인스턴스를 생성하지않고도 메서드에 접근할 수 있습니다.

그럼 어떨 때 정적 클래스를 만들어 주어야할까요?

<br>

## 일반 클래스

일반 클래스로 선언해봅시다.

클래스 인스턴스 생성없이 바로 접근할 경우, 정적 메서드나 변수에만 접근이 가능합니다.

인스턴스를 생성할 경우 인스턴스 메서드나 변수에만 접근이 가능합니다.

```c#
namespace TestCSharp
{
    public class Test2
    {
        public static int i = 0;
        public int j = 0;

        public static void testM1()
        {
            Console.WriteLine("Test");
        }

        public void testM2()
        {
            Console.WriteLine("Test2");
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
            Test2.i = 0;
            Test2.testM1();

            Test2 t2 = new Test2();
            int temp = t2.j;
            t2.testM2();
        }
    }
}
```

<br>

## 정적 클래스

기존에 인스턴스 변수들은 빨간줄이 생기며 사용이 불가능합니다.

즉, static 선언이 되었으면, 그 내부에는 static한 변수나 메소드만 사용할 수 있습니다.


```c#
namespace TestCSharp
{
    public static class Test2
    {
        public static int i = 0;
        //public int j = 0;

        public static void testM1()
        {
            Console.WriteLine("Test");
        }

        /*public void testM2()
        {
            Console.WriteLine("Test2");
        }*/
    }

    class Program
    {
        static void Main(string[] args)
        {
            Test2.i = 0;
            Test2.testM1();

            /*Test2 t2 = new Test2();
            int temp = t2.j;
            t2.testM2();*/
        }
    }
}
```

<br>

## 메소드 내부는?

그렇다면 메소드 내부에 변수를 사용할 경우 어떻게 될까요?

네.. 함수는 클래스와 다릅니다. 말그대로 함수이기 때문에 정적 변수는 할당할 수 없고, 내부에서만 동작하는 일반 변수만을 할당할 수 있습니다.

```c#
public static class Test2
{
    public static void testM1()
    {
        Console.WriteLine("Test");
        string test = "test";
        //static string test2 = "test2";
    }
}
```

<br>

## 결론

학부생 2학년때 배웠던 내용인데 왜 이 내용을 다시 정리할까요..?

무작정 코딩하다보니 엇!? 하며 혼란스러울 때가 가끔씩 있습니다.

다 아는 내용이지만 계속된 학습으로 이루어진 수 많은 지식으로 인해서 말도안되게 더 깊게 생각해버리는거죠.

전공자에겐 되게 간단한 이론인데 말이죠..

그래서 혹여나 추후에 잊어버리지 않게 정리합니다.

