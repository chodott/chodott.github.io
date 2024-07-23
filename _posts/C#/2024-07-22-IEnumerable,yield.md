---
title: C# - IEnumerator/IEnumerable, yield

categories:
  - C# 개념
 
tags:
  - [문법]

date: 2024-07-22

toc: true
toc_sticky: true
sidebar_main: true
---

이 글은 작성자가 개인적으로 공부한 내용을 정리한 글입니다. <br>
따라서 사실과는 다른 내용이 포함될 수 있습니다.  <br>
잘못된 정보가 있다면 메일이나 댓글로 지적해주시면 정말 감사하겠습니다.
{: .notice--primary }

yield 키워드는 컬렉션 데이터를 하나씩 반환하거나 반복 종료를 명시적으로 알리는 용도로 사용합니다.




## <mark style = "background-color : #dcffe4"> IEnumerator(열거자)

### - 개념
IEnumerator는 제네릭이 아닌 모든 열거자의 기본 인터페이스이다. <br>
따라서 인터페이스에 정의되어 있는 다음 3개의 함수를 구현해야 한다.

- Current <br>
읽기 전용 프로퍼티로 현재 위치의 데이터를 object 타입으로 리턴한다.

- MoveNext<br>
위치 + 1 연산 후 컬렉션의 범위 내부면 true 외부이면 false를 반환한다.

- Reset<br>
위치 정보를 초기 상태로 초기화한다.

### - 예시
```c#
public class MyEnumerator : IEnumerator
{
    private int[] _ints;
    private int _position = -1;
    public MyEnumerator(int[] ints) 
    {
        _ints = ints;
    }

    public bool MoveNext()
    {
        _position++;
        return (_position < _ints.Length);
    }

    public void Reset()
    {
        _position = -1;
    }

    public int Current
    {
        get
        {
            try
            {
                return _ints[_position];
            }
            catch(IndexOutOfRangeException)
            {
                throw new InvalidOperationException();
            }
        }
    }

    object IEnumerator.Current
    {
        get { return Current; }
    }
}
```

## <mark style = "background-color : #dcffe4"> IEnumerable

### 개념
IEnumerable는 열거자를 노출시킬 수 있게 하는 인터페이스이다. <br>
이 인터페이스의 멤버는 명시적으로 호출되지 않고도 foreach 문을 통해 데이터를 순회할 수 있다.

### 예시
```c#
public class MyCollection : IEnumerable
{
    int[] ints =  { 1, 2, 3, 4 };
    public IEnumerator GetEnumerator()
    {
        return new MyEnumerator(ints);
    }

    IEnumerator IEnumerable.GetEnumerator()
    {
       return GetEnumerator();
    }
}
```

### foreach

```c#
internal class Program
{
    public static void Main(string[] args)
    {
        MyCollection collection = new MyCollection();
        foreach(var item in collection)
        {
            Console.WriteLine(item); 
        }
    }
}
```
출력이 잘 되는 것으로 보아 GetEnumerator()함수를 통해 IEnumerator 객체를 리턴 받아 순회할 것이라 생각했지만<br>
정작 코드 상에서 호출하지는 않기 때문에 microsoft learn만 보고서는 이해할 수 없었다. <br>
여러 블로그를 찾아보니 foreach를 사용하면 컴파일러가 다음과 같은 코드로 변경해서 실행한다고 한다.<br>
```c#
internal class Program
{
    public static void Main(string[] args)
    {
        MyCollection collection = new MyCollection();
        for(IEnumerator<int> p = collection.GetEnumerator(); p.MoveNext();)
        {
            Console.WriteLine(item); 
        }
    }
}
```

## <mark style = "background-color : #dcffe4"> yield
### - 개념

yield 키워드를 사용하면 IEnumerable, IEnumerator를 상속받는 객체를 간단히 구현할 수 있다.

yield는 주로 컬렉션의 데이터를 반환하거나, 반복기에서 루프를 빠져 나올 때 사용되는데,<br>
yield return문은 일반적인 return문과는 다르게 마지막으로 수행한 yield return의 위치를 기억한다.<br>
따라서 yield return이 포함된 메서드를 호출하면 최근에 호출됐던 yield return의 다음 명령문을 실행한다.<br>
이를 통해 IEnumerable을 반환하는 메서드를 작성할 수 있다.

이전 예제에서 IEnumerable를 구현하는 클래스를 작성할 때, MoveNext()와 Current 프로퍼티를 구현해야만 했다.<br>
하지만 컴파일러는 yield return문이 있는 IEnumerable을 반환하는 함수를 발견하면 MoveNext와 Current를 자동으로 추가한다.<br>

따라서 yield를 사용하면 두 멤버를 구현할 필요가 없다.


 ### - 예제
 ```c#
 namespace csharpstudy
{
    internal class Program_1
    {
        static IEnumerable Number()
        {
            int position = -1;
            while(true)
            {
                position++;
                yield return position;  //데이터 반환
                if (position >= 10) yield break;    //루프 탈출
            }
        }

        static void Main()
        {
            foreach(int i in Number()) 
            {
                Console.WriteLine(i);
            }
        }
        // 1~10까지 출력됨.
    }
}

 ```


---
참고 <br>
Microsoft Learn, "C# yield", https://learn.microsoft.com/ko-kr/dotnet/csharp/language-reference/statements/yield, 2024.07.22 <br>
{: .notice }
