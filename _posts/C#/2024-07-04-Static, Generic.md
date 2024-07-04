---
title: C# - Static, Generic

categories:
  - C# 개념
 
tags:
  - [문법]

date: 2024-07-04

toc: true
toc_sticky: true
sidebar_main: true
---

이 글은 c++ 사용에 익숙한 작성자가 c#과 차이가 있는 부분에 대한 주관적인 의견을 담은 개인 정리 글입니다. <br>
따라서 사실과는 다른 내용이 포함될 수 있습니다. 잘못된 정보가 있다면 메일이나 댓글로 지적해주시면 감사하겠습니다.
{: .notice--primary }

## <mark style = "background-color : #dcffe4"> static

### - 개념
static은 '정적인'이라는 뜻을 가진 단어이다. <br>
static 키워드를 사용한 클래스, 필드, 메서드 등은 객체와 무관하게 타입 자체에 속하게 되며 이를 정적으로 동작한다고 표현한다.
객체를 생성하여 클래스의 멤버에 접근하는 것과 다르게, 아래 예제처럼 클래스의 이름으로 호출한다.

```c#
class GameObject
{
  public static int num;
}
GameObject.num = 1;
```
메서드 또한 동일하다.

- 특징

  1. 객체는 클래스의 모든 필드에 대한 복사본을 갖지만, static 필드는 전체 메모리 상 한 개이다.
  2. static 키워드가 클래스에 적용되면, 클래스의 모든 구성은 static이어야 한다.
  3. static 클래스의 static 생성자는 클래스가 인스턴스화 되기 전에 호출된다. 


## <mark style = "background-color : #dcffe4"> Generic

### - 개념

Generic(제네릭)은 자료형을 미리 정해두지 않고 사용할 때 자료형을 정의하도록 하는 것이다.

```c#
class GameObject
{
  public int data;
}

class Example
{
  static void Main(string[] args)
  {
    GameObject GO = new GameObject();
    GO.data = 1;
  }
}
```
위 예제에서 data라는 변수에 int값이 아닌 float값을 담아야 한다고 가정했을때,<br>
GameObject 클래스 내의 float형 변수를 생성하거나, float형 멤버 변수를 가지는 클래스를 새로 만드는 선택지가 있다.<br>

제네릭을 이용하면 이런 비효율적인 코드를 해결할 수 있다. <br>
제네릭은 자료형을 미리 정의하지 않기 대문에 T라는 형식 매개변수를 사용한다.

```c#
class GameObject<T>
{
  public T data;
}

class Example
{
  static void Main(string[] args)
  {
    GameObject GO = new GameObject<float>();
    GO.data = 1;
  }
}
```

data의 자료형을 정하지 않고 T라는 타입으로 정의한다.<br>
그리고 클래스 이름 옆에<T>를 작성해 객체를 생성할 때 이 타입을 정의하게 한다. <br>
이를 통해 여러 가지 타입을 유연하게 사용할 수 있게 된다.

## <mark style = "background-color : #dcffe4"> Generic Collection

### 1. object

object 자료형은 모든 int, string, float, class 같은 모든 자료형의 부모 자료형이다. <br>
따라서 object 자료형은 모든 값을 담을 수 있고, 모든 자료형은 object로 형변환 할 수 있다.

자로형들을 일반화시킬 때 object 자료형을 사용하면 되지 않을까? 라는 생각이 당연하게도 떠오른다.
하지만 이는 박싱, 언박싱이라는 과정을 피할 수 없게 되고, 성능에 치명적인 영향을 미치게 된다.

- boxing(박싱)
  
  object 자료형은 참조 형식이다. <br>
  따라서 float, int와 같은 값 형식 자료형을 담을 때, 참조 형식으로 형변환하게 되는데, 이 것이 Boxing(박싱)이다.

- unboxing(언박싱)

  boxing과 반대로 참조 형식인 object 자료형을 값 형식 자료형으로 형변환 하는 것이 unboxing(언박싱)이다.

값 형식과 참조 형식은 각각 스택 메모리와 힙 메모리라는 서로 다른 메모리에 저장된다. <br>
따라서 boxing이 발생하면 힙 메모리를 할당받는 과정이 필요하고, unboxing이 발생하면 <br>힙 메모리에 존재하는 데이터를 스택 영역으로 옮기는 과정이 필요해 성능 저하가 발생한다.


### 2. collection

배열은 선언할 때 지정했던 크기를 변경할 수 없다. <br>
따라서 저장할 데이터에 비해 과도한 메모리를 할당받으면 메모리가 낭비되고, <br>
또 너무 적은 메모리를 할당하면 데이터를 담기 위해 새로운 배열을 생성해야 한다.

또한 데이터를 배열에 추가하거나 삭제 연산 시 모든 데이터의 인덱스를 변경해야 하는 비효율적인 과정이 강제된다.

이러한 단점을 보완하기 위해 c#의 System,Collections라는 namespace에 다양한 타입이 존재한다.

1. ArrayList

2. HashTable

3. queue

4. stack

위의 컬렉션은 object 자료형으로 저장하기 때문에 모든 타입의 값을 담을 수 있지만 <br>
object 문단에서 설명한 것처럼 박싱, 언박싱이 일어나 성능 저하 문제가 발생하게 된다.

### 3. Generic Collection

제네릭 컬렉션을 이용하여 이전의 문제들을 해결할 수 있다. <br>
제네릭 컬렉션을 사용하기 위해서는 Syste.mCollections.Generic 네임스페이스를 사용해야 한다.

앞서 서술했던 컬렉션들은 각각 다음과 같이 대응된다.

1. List<T> - ArrayList
2. Dictionary<T> - HashTable
3. Queue<T> - Queue
4. Stack<T> - Stack

### <mark style = "background-color : #dcffe4"> C++ Template, C# Generic

역할이 비슷한 것 같아, 개인적인 궁금증으로 찾아서 정리해봐야겠다.




---
참고 <br>
백현우,「처음 배우는 C# 프로그래밍」, 위키북스, 2020 <br>
Microsoft Learn, "C# 제네릭", https://learn.microsoft.com/ko-kr/dotnet/csharp/, 2024.07.04 <br>
{: .notice }
