---
title: Unity - 조작(Input)

categories:
  - Unity
 
tags:
  - [Input]

date: 2024-07-10

toc: true
toc_sticky: true
sidebar_main: true
---

이 글은 작성자가 개인적으로 공부한 내용을 정리한 글입니다. <br>
따라서 사실과는 다른 내용이 포함될 수 있습니다.  <br>
잘못된 정보가 있다면 메일이나 댓글로 지적해주시면 정말 감사하겠습니다.
{: .notice--primary }

## <mark style = "background-color : #dcffe4"> PlayerController


### - PlayerController.cs
오브젝트에 부착하여 플레이어의 키입력을 받아 오브젝트를 이동시킬 컴포넌트를 구현했다.

```c#
public class PlayerController : MonoBehaviour
{
  private Vector3 aimDir = Vector3.zero;
  private Vector3 moveDir = Vector3.zero;
  public Rigidbody rigidbody;

  private float CurrentDir;
  private float Speed = 5.0f;
  

  private void Start()
  {
    rigidbody = GetComponent<Rigidbody>();
  }

  public void Update()
  { 
    aimDir.x = Input.GetAxis("Mouse X");
    aimDir.z = Input.GetAxis("Mouse Y");
    moveDir.x = Input.GetAxis("Horizontal");
    aimDir.Normalize();
    moveDir.Normalize();

  }

  private void FixedUpdate()
  {
    if(aimDir != Vector3.zero)
    {
      transform.forward = Vector3.Lerp(transform.forward, aimDir, Time.deltaTime * 5.0f);
    }
    if(moveDir != Vector3.zero)
    {
      rigidbody.MovePosition(transform.position + Time.deltaTime * Speed * moveDir);
    }
  }
}
```

#### - Rigidbody
Rigidbody는 GameObject가 물리 제어로 동작하게 하는 컴포넌트이다. <br>
나의 경우에는 캐릭터에게 중력의 영향을 주기 위해서 사용하였다.

#### - Input
Input 클래스를 사용하여 Input Manager에서 설정한 입력과 축을 통해 행동을 정의할 수 있다. <br><br>
Horizontal 축은 수평 방향에 대해 -1 +1 사이의 float 값을 리턴하고, A와 D가 디폴트로 맵핑되어 있다.<br>
따라서 `moveDir.x = Input.GetAxis("Horizontal");` 문장으로 이동할 방향을 설정하였다. <br> <br>
Mouse X, Mouse Y축은 각각 마우스 커서 위치의 수평, 수직 값을 반환한다. <br>
이를 aim_dir의 x와 z값을 초기화하여 오브젝트가 바라볼 방향 벡터를 구했다. <br><br>
InputManager는 아래 이미지와 같이 Project Setting을 통해 설정할 수 있다. <br>
![image](https://github.com/chodott/chodott.github.io/assets/89974193/f048c23d-5d3c-4917-815b-df826bf107f2)<br>

#### - Update 함수
매 프레임마다 Input의 입력에 따른 aimDir, moveDir의 값을 초기화하고, 정규화한다.

#### - FixedUpdate 함수
매 프레임마다 호출되는 Update 함수와 다르게 FixedUpdate는 호출 간격이 일정하다고 한다. <br>
Edit -> Setting -> Time -> Fixed Timestep에서 수정할 수 있고, 디폴트 값은 0.02이다. <br>
Unity Documentation에서는 rigidbody를 사용할 때 FixedUpdate를 사용하라고 권장하는데, <br>
물리 연산은 일정한 주기로 일정한 처리를 해야하기 때문에 적합하다고 볼 수 있다. <br><br>
aimDir이 영벡터가 아닐 때 오브젝트를 Lerp 함수를 이용해 회전시켰고,<br>
moveDir이 0벡터가 아닐 때 MovePosition 함수로 위치를 이동시켰다. 

---

## <mark style = "background-color : #dcffe4"> 결과
<iframe width="1143" height="643" src="https://www.youtube.com/embed/5NVVF1lUGSI?list=PL2SQ-5JLR4QnWrcieKZCMDFdZuMQoDxbQ" title="Unity-Toyproject-조작" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>