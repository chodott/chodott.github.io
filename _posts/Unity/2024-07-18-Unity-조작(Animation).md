---
title: Unity - 조작(Animation)

categories:
  - Unity
 
tags:
  - [Animation]

date: 2024-07-18

toc: true
toc_sticky: true
sidebar_main: true
---

이 글은 작성자가 개인적으로 공부한 내용을 정리한 글입니다. <br>
따라서 사실과는 다른 내용이 포함될 수 있습니다.  <br>
잘못된 정보가 있다면 메일이나 댓글로 지적해주시면 정말 감사하겠습니다.
{: .notice--primary }

이번에는 캐릭터의 조작에 따라 애니메이션을 반영해보려고 한다.

## <mark style = "background-color : #dcffe4"> Unity
### 1. 이동과 점프
게임 오브젝트에 애니메이션을 지정하기 위해서는 Animator 컴포넌트를 사용하면 된다. <br>
또한 어떤 애니메이션을 사용할 것인지, 언제 어떤 애니메이션으로 전환하고 블렌딩할 것인지를 정의하려면 <br> Animator Controller를 생성하고 StateMachine을 작성하여 연결해야 한다. <br>

다음은 내가 작성한 Animator Controller의 Base Layer 상태 머신이다. <br>
![image](https://github.com/user-attachments/assets/b70c179b-d1a3-4cc2-8e2a-5603463a4381)

우선 이동과 점프 먼저 구현하였다.

OnGround <-> Jump 상태 간의 전환은 bool형 isOnGround 파라미터에 의해 이루어진다. <br>
이전에 PlayerController 클래스에서 허공에서의 점프를 막기 위해 작성했던 isOnGround 변수를 이용해 
초기화한다. <br>

#### - PlayerController의 CheckOnGround()
```c#
private void CheckOnGround()
{
    RaycastHit hit;
    if (Physics.Raycast(transform.position + (Vector3.up * 0.2f), Vector3.down, out hit, 0.4f, layer))
    {
         isOnGround = true;
    }
    else isOnGround = false;
    animator.SetBool("isOnGround", isOnGround);
}
```

다음은 블렌드 트리 'OnGround'의 내부 구조이다.<br>
![image](https://github.com/user-attachments/assets/4eb23fe6-3b9d-44d4-bf57-a2cd9ab08868)

float형 파라미터 idle_run_ratio으로 idle과 run 사이 애니메이션을 블렌딩했고, <br>
float형 파라미터 move_direction으로 방향에 따른 run 애니메이션을 블렌딩했다. <br>

move_direction은 bool형으로 해결하려 했으나, 블렌딩 트리에서는 float형 파라미터만 사용 가능했다.<br>
따라서 현재 캐릭터가 바라보는 transform.forward의 x값의 부호와 aimDir.x값의 부호를 곱 연산해<br>
같은 방향을 바라보면 1, 아닐 때는 -1로 초기화하여 전방, 후방 이동을 완성하였다.


#### - PlayerController의 Update()
(우선 필드에 animator를 선언하고 start 함수내에서 참조해야 한다.)
```c#
public void Update()
{
    aimDir.Normalize();
    moveDir.Normalize();

    if(Input.GetButtonDown("Jump") && isOnGround)
    {
        rigidbody.AddForce(Vector3.up * 5.0f, ForceMode.VelocityChange);
        animator.SetBool("OnGround", false);
    }

    idle_run_ratio = Math.Abs(velocity / MaxSpeed);
    //animator 파라미터 초기화
    animator.SetFloat("idle_run_ratio", idle_run_ratio); 
    animator.SetFloat("move_direction", MathF.Sign(transform.position.x) * MathF.Sign(aimDir.x));
}
```

### 2. 조준

내가 다운받은 에셋의 총구를 조준하는 애니메이션은 하체가 idle과 같이 고정되어 있다. <br>
하지만 서있을 때만 총을 발사하지는 않을 것이기에 Avatar Mask를 통해 상하체가 다른 애니메이션을 출력한다. <br>

Layer Mask는 다음과 같이 Humanoid body 다이어그램에서 부위를 선택 및 해제하여 마스킹 위치를 표시할 수 있다.<br>

![image](https://github.com/user-attachments/assets/d321bc62-7c50-4c8a-9c30-ea2a97a6c1ca)

run 애니메이션이 출력되어야 하는 하체를 제외한 부위를 선택하였고 다음과 같이 Upper Layer에 반영하였다. <br>

![image](https://github.com/user-attachments/assets/02076a9a-800a-40e0-a332-e780450353e5)

다운받은 애니메이션이 위에서 정면까지 그리고 정면에서 아래까지 조준하는 것으로 나누어져 있었다. <br>
따라서 aim_direction 파라미터를 선언해 PlayerController의 멤버 변수 aimDir의 z값으로 초기화하여 <br>
조이스틱 조작에 따라 원하는 곳을 조준하는 애니메이션을 완성하였다.

#### - PlayerController의 Turn()
```c#
public void Turn(Vector2 directionVector)
{
    aimDir.x = directionVector.x;
    aimDir.z = directionVector.y;

    Vector3 forwardVector = new Vector3(aimDir.x, 0, 0);
    forwardVector.Normalize();
    transform.forward = Vector3.Lerp(transform.forward, forwardVector, 0.5f);

    animator.SetFloat("aim_direction", aimDir.z);
}
```


## <mark style = "background-color : #dcffe4"> 결과
<iframe width="1236" height="695" src="https://www.youtube.com/embed/0vaOt4AgKbk" title="Unity-Toyproject_조작_4(Animation)" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>