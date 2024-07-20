---
title: C# - Action / Func

categories:
  - C# 개념
 
tags:
  - [문법]

date: 2024-07-20

toc: true
toc_sticky: true
sidebar_main: true
---

이 글은 작성자가 개인적으로 공부한 내용을 정리한 글입니다. <br>
따라서 사실과는 다른 내용이 포함될 수 있습니다.  <br>
잘못된 정보가 있다면 메일이나 댓글로 지적해주시면 정말 감사하겠습니다.
{: .notice--primary }

Action과 Func는 이전에 다뤘던 무명 메서드와 람다식처럼 delegate에 메서드를 담아 전달하는 과정을 간단히 하는 역할을 한다. <br>


## <mark style = "background-color : #dcffe4"> Action

### - 개념
Action은 System 네임스페이스에 정의되어 있는 델리게이트 변수이다.<br>
반환 값이 없는 메서드를 참조할 때 Action을 사용한다.

다음은 문자열을 콘솔 창에 출력하는 메서드를 델리게이트에 담아 전달하는 예시이다.

```c#
internal class Program
{
    delegate void printDelegate(string text);

    public static void Main(string[] args)
    {
      printDelegate del = Print;
      del("hi");
    }

    public static void Print(string text)
    {
      Console.WriteLine(text);
    }
}
```

### - 예제
위 예시를 Action을 이용하면 다음과 같이 델리게이트를 정의하지 않고 작성할 수 있다.
```c#
 internal class Program
{
    public static void Main(string[] args)
    {
        Action<string> act = Print;
    }
    public static void Print(string text)
    {
        Console.WriteLine(text);
    }
}
```
Print 함수는 int 인수를 받기 떄문에 제네릭을 이용해 인수의 타입을 Action 옆에 명시한다. <br>
하지만 인수가 필요 없을 경우에는 생략하면 된다.


## <mark style = "background-color : #dcffe4"> Func
### - 개념
Func 또한 System 네임스페이스에 정의된 델리게이트 변수이다. <br>
Action과 반대로 반환 값이 있는 메서드를 참조 할 때 Func 델리게이트를 사용한다. <br>
Func 델리게이트의 반환 값도 제네릭을 사용해 명시해야 하는데, <br>
인수와 반환 값 모두 제네릭으로 명시해야 할 경우 앞에 명시하는 타입이 인수이고 마지막에 명시하는 타입이 반환 값이다.

정수를 인수로 받아 제곱을 반환하는 메서드를 Func를 이용해 작성하면 다음과 같다

### - 예제 <br>
```c#
internal class Program
{
    public static void Main(string[] args)
    {
        Func<int, int> func = Pow;  //첫번쨰 int는 인수, 마지막 int는 반환 타입
        Console.WriteLine(func(5));

    }
    public static int Pow(int num)
    {
        return num * num;
    }
}
```

## <mark style = "background-color : #dcffe4"> + 람다식 활용

### - 예제<br>
앞서 Action과 Func를 이용한 예시에 람다식을 이용해 더 간단하게 표현할 수 있다.

```c#
internal class Program
{
    public static void Main(string[] args)
    {
        Action<string> act  = (text) => { Console.WriteLine(text); };
        Func<int, int> func = (num) => { return num * num; };
        act("hi");
        Console.WriteLine(func(5));
    }
}
```


---
참고 <br>
백현우,「처음 배우는 C# 프로그래밍」, 위키북스, 2020 <br>
Microsoft Learn, "C# 제네릭", https://learn.microsoft.com/ko-kr/dotnet/csharp/, 2024.07.04 <br>
{: .notice }
