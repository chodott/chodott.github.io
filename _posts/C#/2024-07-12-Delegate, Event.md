---
title: C# - Delegate / Event

categories:
  - C# 개념
 
tags:
  - [문법]

date: 2024-07-12

toc: true
toc_sticky: true
sidebar_main: true
---

이 글은 작성자가 개인적으로 공부한 내용을 정리한 글입니다. <br>
따라서 사실과는 다른 내용이 포함될 수 있습니다.  <br>
잘못된 정보가 있다면 메일이나 댓글로 지적해주시면 정말 감사하겠습니다.
{: .notice--primary }

## <mark style = "background-color : #dcffe4"> Delegate

### - 개념
Delegate(델리게이트)는 메서드를 담는 타입의 일종이다. 

delegate를 선언하는 방법은 아래의 형식과 같다. <br>
**delegate 반환형 델리게이트이름(매개변수);** <br>

다음은 예시이다. <br>
```c# 
delegate void PrintDelegate();
```

위와 같이 델리게이트를 선언했다면 담을 메서드는 아래의 PrintHello 함수처럼 반환 타입과 매개변수가 일치해야 한다.

```c#
PrintDelegate del = new PrintDelegate(PrintHello);

void PrintHello()
{
  Console.WriteLine("Hellos");
}
```
이외에 간편 표기법으로 다음과 같이 대입할 수도 있다. <br>
```c# 
PrintDelegate del = PrintHello;
```

### - Multicast
델리게이트는 여러 개의 메서드를 담을 수 있는 기능인 **Multicast(멀티캐스트**) 기능을 지원한다. <br>
+= 연산자를 사용해 메서드를 추가하고, -= 연산자를 사용해 추가한 메서드를 제거할 수 있다. <br>
다음은 예시이다.

```c#
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace csharpstudy
{
    internal class Program
    {
        delegate void PrintDelegate();
        static void Main(string[] args)
        {
            PrintDelegate del = PrintHello;
            del += PrintWorld;

            del();
        }

        static void PrintHello()
        {
            Console.WriteLine("Hello");
        }

        static void PrintWorld()
        {
            Console.WriteLine("World");
        }
    }
}
```
- 실행 결과 <br>
![image](https://github.com/user-attachments/assets/11075434-b646-4bb3-99ef-5b17664f4411)


### - Callback
델리게이트는 변수이므로 인수로 메서드를 전달하여, 호출 당한 메서드에서 호출한 쪽의 메서드를 호출할 수 있다. <br>
예시로 사장이 노동자에게 월급을 주고, 노동자가 확인 후에 사장에게 보고하는 상황을 클래스로 표현하면 다음과 같다. <br>

```c#
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace csharpstudy
{
    internal class Program
    {
        delegate void PrintDelegate();
        static void Main(string[] args)
        {
            Boss boss = new Boss();
            Worker worker = new Worker();
            boss.PaySalary(worker);
        }

      class Boss
        {
            public void PaySalary(Worker worker)
            {
                PrintDelegate del = FinishPay;
                worker.Check(del);
            }
            public void FinishPay()
            {
                Console.WriteLine("Done");
            }
        }

       class Worker
        {
            public void Check(PrintDelegate del)
            {
                del();
            }
        }
    }
}
```

일반적으로는 Worker클래스의 Check함수의 매개변수로 boss객체를 받아 boss의 FinishPay 함수를 호출하겠지만, <br>
델리게이트를 이용해 전달된 메서드를 호출하도록 할 수 있었다.


## <mark style = "background-color : #dcffe4"> Event

### - 개념

Event(이벤트)는 앞서 배운 델리게이트를 이용한 콜백을 좀 더 기능적으로 만들어주는 키워드이다. <br>
이벤트를 이용하면 이전 예제에서의 델리게이트 변수를 넘기는 과정을 생략할 수 있다. <br>

이벤트를 보내거나 발생시키는 클래스를 Publisher이고, 받거나 처리하는 클래스는 Subscriber이다. <br>
Publisher가 이벤트를 발생시키면 구독되어있는 여러 Subscriber에게 이를 알려줄 수 있다.

다음은 이벤트 사용 예시이다.

```c#
using System;
using System.Collections;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace csharpstudy
{
    internal class Program
    {
        delegate void EventHandler();
        static void Main(string[] args)
        {
            Publisher publisher = new Publisher();
            for (int i = 0; i < 5; ++i) new Subscriber();
            publisher.RunEvent();
        }

        class Publisher
        {
            public static event EventHandler myEvent;

            public void RunEvent()
            {
                if (myEvent == null) return; // 1
                myEvent();
            }
        }

        class Subscriber
        {
            public Subscriber()
            {
                Publisher.myEvent += DoByEvent; // 2
            }
            public void DoByEvent()
            {
                Console.WriteLine("이벤트 발생 시 무언가!");
            }
        }
    }
}
```
- 부가 설명<br>
1. Publisher의 이벤트를 구독한 클래스가 없다면 myEvent를 호출할 때 NullReferenceException이 발생한다. <br>
따라서 이를 방지하기 위해 if문을 사용한다.
2. Subscriber의 생성자에서 static으로 선언된 Publisher의 myEvent를 구독한다.

- 출력 결과 <br>
![image](https://github.com/user-attachments/assets/b9a4cc76-1663-43b6-af47-374e846907cf) <br>
Main함수의 for문으로 생성된 Subscriber 객체의 개수만큼 출력되는 것을 볼 수 있다.






---
참고 <br>
백현우,「처음 배우는 C# 프로그래밍」, 위키북스, 2020 <br>
Microsoft Learn, "C# 제네릭", https://learn.microsoft.com/ko-kr/dotnet/csharp/, 2024.07.04 <br>
{: .notice }
