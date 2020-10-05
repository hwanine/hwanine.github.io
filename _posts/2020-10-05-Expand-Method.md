---
toc: true
toc_sticky: true
categories:
  - C#
title: C# - 확장메서드
tags: [C#, WPF, MVVM]
excerpt: "C# 확장메서드 튜토리얼"
---

# 확장메서드

## 확장메서드란?

확장메서드를 이용하면 기존에 사용하던 클래스를 상속받아서 메소드를 추가하지않고, 기능을 확장해서 사용할 수 있습니다.

이 기능은 .Net 3.5부터 새로 추가된 기능입니다.

우선 주의해야할 점은 다음과 같습니다.

- 확장메서드는 정적 클래스의 멤버여야한다.
  
- 확장메서드의 첫번째 매개변수는 this 키워드를 한정자로 사용해야한다.

- 객체의 인스턴스를 통하고나 정적클래스를 통해 정적으로 호출할 수 있다.

<br>

## Case 1

아래의 코드를 보면 int형 멤버 함수들을 사용할 수 있는 것을 볼 수 있습니다.

![image](https://user-images.githubusercontent.com/57826388/95091582-96e8e680-0761-11eb-9043-f6f7bc11f233.png)

<br>

이제 int 타입 확장 메스드를 추가해보겠습니다.

다음과 같이 확장 메서드를 호출할 수 있습니다.

![image](https://user-images.githubusercontent.com/57826388/95092045-25f5fe80-0762-11eb-91d1-c5f523cc39fa.png)

<br>

여기서 this로 정해진 타입이 매개변수라기보단 확장메서드가 확장하고자 하는 타입으로 보면 됩니다.

별도의 매개변수를 더 추가하고 싶으면 다음의 코드처럼 this 뒤에 매개변수 항목을 추가하면 됩니다.

```c#
namespace TestCSharp
{
    public static class MyExtension
    {
        public static void MyInt(this int n, int m)
        {
            Console.WriteLine("int 출력" + n + m);
        }
    }
    class Program
    {
        static void Main(string[] args)
        {
            int n = 100;
            n.MyInt(5);
        }
    }
}
```

<br>

## Case 2


역시 마찬가지로 List같은 다른 형식도 다음과 같이 사용할 수 있습니다.

```c#
namespace TestCSharp
{
    public static class MyExtension
    {
        public static void MyIntList(this List<int> n)
        {
            foreach (int i in n)
                Console.WriteLine("int List 출력" + i);
        }
    }
    class Program
    {
        static void Main(string[] args)
        {
            List<int> list = new List<int>();
            list.Add(1);
            list.Add(10);
            list.MyIntList();
        }
    }
}
```

<br>

## case 3

다음과 같이 기존에 존재하는 클래스에 함수를 확장하여 사용할 수 있습니다.

```c#
namespace TestCSharp
{
    public class MyClass
    {
        public int speed;
        public void AddSpeed(int s) { this.speed += s; }
    }
    public static class MyExtension
    {
        public static void MinusSpeed(this MyClass cls, int s)
        {
            cls.speed -= s;
        }
    }
    class Program
    {
        static void Main(string[] args)
        {
            MyClass mc = new MyClass();
            mc.AddSpeed(100);
            mc.MinusSpeed(50);
        }
    }
}
```

<br>

## 참고자료

<https://durubiz.tistory.com/entry/Net-C-%ED%99%95%EC%9E%A5%EB%A9%94%EC%84%9C%EB%93%9C-extension-method>