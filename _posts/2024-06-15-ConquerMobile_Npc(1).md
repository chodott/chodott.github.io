---
title: 모바일 정복 프로젝트(3) - Npc

categories:
  - Unity
 
tags:
  - [Unity, C#]

date: 2024-06-14
---

## Npc

![image](https://github.com/chodott/chodott.github.io/assets/89974193/8af7765e-bdfc-472f-8682-f6f88c0703e3){: width="70%" height="70%"}

위 게임은 <만져라 메이드 인 와리오> 라는 닌텐도가 개발한 게임의 로비 화면이다. <br>
로비에 서 있는 Npc들을 터치하면 그 Npc가 담당하는 방식의 미니게임들을 진행할 수 있는데, <br>
내 프로젝트에서 비슷하게 채택해보고자 한다.

> 우선적으로 Npc를 터치하는 기능을 만들자,
---

### PlayerController.cs

```C#
using System.Collections;
using System.Collections.Generic;
using Unity.VisualScripting;
using UnityEngine;
using UnityEngine.UIElements;

public class PlayerController : MonoBehaviour
{
    // Start is called before the first frame update
    void Start()
    {
        GameManager.Input.KeyAction -= OnMouseClicked;
        GameManager.Input.KeyAction += OnMouseClicked;
    }

    // Update is called once per frame
    void Update()
    {
        
    }

    void OnMouseClicked()
    {
        if(Input.GetMouseButtonDown(0))
        {
            Ray ray = Camera.main.ScreenPointToRay(Input.mousePosition);
            RaycastHit hit;
            if(Physics.Raycast(ray, out hit))
            {
                Npc npc = hit.transform.GetComponent<Npc>();
                if (npc != null)
                {
                    npc.OnClicked();
                }
            }
        }
    }
}
```

- OnMouseClicked()
    - Input.GetMouseButtonDown(0)<br>
    왼쪽 마우스가 클릭되었는지 체크하는 함수이다.<br>

        | 함수 | 역할 |
        | ------------ | ------------- |
        | GetMouseButtonDown(0) | 왼쪽 마우스 클릭  |
        | GetMouseButtonDown(1) | 오른쪽 마우스 클릭  |
        | GetMouseButtonDown(2) | 마우스 중앙 클릭  |

    - Raycast를 통한 충돌 <br>
    왼쪽 마우스가 클릭했을 때 스크린에서 마우스의 위치를 시작점으로 Ray를 발사한다.<br> 
    Ray와 충돌이 발생한 객체의 Transform 참조를 얻어 Npc 컴포넌트를 갖고 있다면, <br>
    Npc의 OnClicked를 호출하도록 작성했다.

---

Npc가 클릭되었을 때 바로 게임 시작 버튼이 뜨는게 아니라 카메라가 Npc의 정면을 볼 수 있는 위치로 이동도록 구현하고 싶었다. <br>
따라서 시네머신을 이용해 카메라 블렌딩을 구현해보았다.


- Npc 오브젝트에 가상카메라 위치를 나타낼 CameraPos 오브젝트를 하위로 뒀다.   
아래 사진과 같이 CinemachineVirtualCamera 컴포넌트의 옵션 중 Follow를 CameraPos의 Transform으로, <br>Look At을 Npc 오브젝트의 Transform으로 두어
Npc오브젝트의 근처에서 Npc오브젝트를 바라보는 가상카메라를 배치했다.

- CinemachineVirtualCamera 컴포넌트를 배치하면 기본적으로 존재하는 MainCamera에 CinemachineBrain 컴포넌트가 자동으로 추가된다

    ![image](https://github.com/chodott/chodott.github.io/assets/89974193/38f00937-c19a-43a3-a4b2-088e96047133){: width="49%"}![image](https://github.com/chodott/chodott.github.io/assets/89974193/7faa6734-d58f-4034-9a25-ebcc8362f5ee){: width="49%"}
 
    


### Npc

```C#
using Cinemachine;
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.UI;

public class Npc : MonoBehaviour
{
    // Start is called before the first frame update
    void Start()
    {
        
    }

    // Update is called once per frame
    void Update()
    {
        
    }

    public void OnClicked()
    {
        CinemachineVirtualCamera camera = GetComponentInChildren<CinemachineVirtualCamera>();
        camera.MoveToTopOfPrioritySubqueue();
    }
}
```
- OnClicked()
    - *camera.MoveToTopOfPrioritySubQueue();*   
    위는 가장 높은 우선순위가 동일한 가상 카메라가 여러 개일 경우, 해당 카메라를 Live되게 하는 함수이다. <br>
    이를 통해 OnClicked가 호출되면 Npc오브젝트를 바라보는 가상 카메라로 블렌딩이 가능해졌다.




--- 
## 결과물
- 메인 카메라 시점(좌), 가상 카메라 시점(우)

![image](https://github.com/chodott/chodott.github.io/assets/89974193/29ffd667-761b-4d82-a7b7-e3e17c39d6d6){: width="45%"}![image](https://github.com/chodott/chodott.github.io/assets/89974193/a713a890-74e3-427e-8dd8-ceca49c6b0b7){: width="45%"}

 

