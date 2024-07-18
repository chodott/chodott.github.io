---
title: Unity - 조작(Virtual Joystick)

categories:
  - Unity
 
tags:
  - [Virtual Joystick]

date: 2024-07-16

toc: true
toc_sticky: true
sidebar_main: true
---

이 글은 작성자가 개인적으로 공부한 내용을 정리한 글입니다. <br>
따라서 사실과는 다른 내용이 포함될 수 있습니다.  <br>
잘못된 정보가 있다면 메일이나 댓글로 지적해주시면 정말 감사하겠습니다.
{: .notice--primary }

현재 제작하고 있는 게임은 키보드 키 입력을 받고 있다. <br>
하지만 모바일 게임으로 기획되었기에, 가상 조이스틱을 통한 조작으로 변경하고자 한다. <br>

## <mark style = "background-color : #dcffe4"> 새로 작성된 클래스

### - VirtualJoystick.cs
객체의 상태를 관리하는 클래스.

```c#
public class VirtualJoystick : MonoBehaviour, IBeginDragHandler, IDragHandler, IEndDragHandler
{
    private RectTransform lever;
    private RectTransform rectTransform;

    [SerializeField, Range(10, 150)]
    private float leverRange;
    [SerializeField]
    private type joystickType;

    private Vector2 directionVector;
    private bool isInput;
    public PlayerController playerController;

    public enum type
    {
        circle, horizontal
    }

    private void Awake()
    {
        rectTransform = GetComponent<RectTransform>();
        lever = transform.GetChild(0).GetComponent<RectTransform>();
    }

    public void OnBeginDrag(PointerEventData eventData)
    {
        AdjustLever(eventData);
    }

    public void OnDrag(PointerEventData eventData)
    {
        AdjustLever(eventData);
    }

    public void OnEndDrag(PointerEventData eventData)
    {
        lever.anchoredPosition = Vector2.zero;
        isInput = false;
    }

    public void AdjustLever(PointerEventData eventData)
    {
        Vector2 inputPos = eventData.position - rectTransform.anchoredPosition;
        if (joystickType == type.horizontal) inputPos.y = 0;
        Vector2 inputVector = inputPos.magnitude < leverRange ? 
                              inputPos : inputPos.normalized * leverRange;
        lever.anchoredPosition = inputVector;

        directionVector = inputPos;
        directionVector.Normalize();
        isInput = true;
    }

    void Update()
    {
        if (!isInput)
        {
            if (joystickType == type.horizontal)
            {
                playerController.Stop();
            }
            
            return;
        }

        if(joystickType == type.horizontal) 
        {
            playerController.Run(directionVector);
        }

        else
        {
            playerController.Turn(directionVector);
        }
    }
}

```
#### - enum type
조이스틱의 모양(원형, 가로 타원형)의 따른 동작을 구분하기 위해 선언

#### - EventSystems
VirtualJoystick 클래스가 상속받은 IBeginDragHandler, IDragHandler, IDragEndHandler는 <br>
각각 드래그 시작 직전, 드래그되는 동안, 드래그가 종료했을 때 콜백 함수를 호출하기 위해 사용하는 인터페이스이다. <br>
따라서 반드시 각각 OnBeginDrag, OnDrag, OnEndDrag 함수를 구현해야 한다.

#### - AdjustLever
오브젝트가 드래그될 때 호출되는 함수이다. <br>
UI 오브젝트는 Rect Transform 컴포넌트를 통해 스크린 상에서의 위치를 얻을 수 있다. <br>
anchoredPosition은 앵커를 (0,0)으로 했을 때의 오브젝트의 위치이다. <br>
따라서 eventData.position을 rectTransform.anchoredPosition으로 빼게 되면 <br>
오브젝트의 center, 즉 조이스틱의 중앙에서 input의 위치 벡터를 얻을 수 있다. <br>

위치 벡터의 크기가 leverRange보다 크면 정규화하여 leverRange를 곱해 레버의 이동 범위를 제한한다. <br>
위치 벡터를 정규화하여 멤버 변수 directionVector를 초기화하는데, 이는 캐릭터 조작에 쓰인다.

앵커는 Rect Transform에서 설정할 수 있다. <br>
![image](https://github.com/user-attachments/assets/6df61190-8161-4dc7-aaa6-4b47d0a2e247)



#### - Update
VirtualJoystick에 입력이 있을 때 joystickType에 맞게 PlayerController의 동작(Run, Turn)을 호출한다. <br>
AdjustLever를 통해 구한 directionVector를 인자로 넘긴다.

## <mark style = "background-color : #dcffe4"> 기존 클래스

### - PlayerController.cs
VirtualJoystick에서 구한 directionVector를 바탕으로 캐릭터의 이동, 회전 방향 초기화 <br>
(새로 추가된 함수만 반영했습니다.)
```c#
public class PlayerController : MonoBehaviour
{
   public void Run(Vector2 directionVector)
    {
        _stateContext.Transition(_moveState);
        moveDir.x = directionVector.x;
    }
    public void Stop()
    {
        _stateContext.Transition(_idleState);
        moveDir.x = 0;
    }

    public void Turn(Vector2 directionVector)
    {
        aimDir.x = directionVector.x;
        aimDir.z = directionVector.y;
    }
}
```
## <mark style = "background-color : #dcffe4"> Unity
### - Hiearchy
GameObject -> UI -> Image를 생성하여 Joystick이라는 이름을 붙이고, <br>
하위에 Image를 생성하여 Lever라는 이름을 붙였다. <br>
![image](https://github.com/user-attachments/assets/d9d2fcd9-34a1-4980-8a2e-3734834b750a)

### - lnspector
Joystick 오브젝트에 VirtualJoystick 컴포넌트를 넣는다. LeverRange와 Joystick Type을 설정하고, <br> PlayerController에 PlayerController 컴포넌트를 가진 오브젝트를 참조한다.
![image](https://github.com/user-attachments/assets/73faf880-459b-4cde-b016-c92eb6966a22)


---

## <mark style = "background-color : #dcffe4"> 이슈
결과물을 녹화하기 위해 전체화면으로 전환하였더니..
![image](https://github.com/user-attachments/assets/72dd0cd1-fa8f-4249-b4c0-9478921c9853)

위와 같이 우측 하단에 있어야 할 조이스틱의 위치가 중앙에 있다. <br>
좌측 하단 앵커를 기준으로 위치가 설정되어 변경된 해상도에 따라 위치가 수정되지 못한 오류로 보인다.

### VirtualJoystick.cs - AdjustLever
```c#
   public void AdjustLever(PointerEventData eventData)
    {
        Vector2 inputPos;
        if (joystickType == type.horizontal)
        {
            inputPos = eventData.position - rectTransform.anchoredPosition;
            inputPos.y = 0;
        }
        else
        {   //추가된 부분
            inputPos = new Vector2(-Screen.width + eventData.position.x, eventData.position.y) - rectTransform.anchoredPosition;
        }
        Vector2 inputVector = inputPos.magnitude < leverRange ? 
        inputPos : inputPos.normalized * leverRange;
        lever.anchoredPosition = inputVector;

        directionVector = inputPos;
        directionVector.Normalize();
        isInput = true;
    }
```
joystickType이 circle일 때 우측 하단 앵커 기준으르 eventData.position의 위치를 구하여 해결하였다.

## <mark style = "background-color : #dcffe4"> 결과
<iframe width="1236" height="695" src="https://www.youtube.com/embed/r7s5-ordYVs" title="Unity-Toyproject-조작_3(가상 조이스틱)" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>