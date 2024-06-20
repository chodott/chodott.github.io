---
title: 모바일 정복 프로젝트(2) - InputManager

categories:
  - Unity
 
tags:
  - [Unity]

date: 2024-06-14
---

## InputManager
- 입력 관리자

력을 관리하기 위해서, 또 블로그들을 순회하다 보니 일반적으로 두가지 방법이 있어 보였다.

첫번쨰 방법은 Update() 함수 내에서 각 Key 입력을 검사하는 방법이고, <br>
두번째 방법은 입력을 총괄 관리하는 Input Mamager를 따로 제작하는 것이다 <br> 
전자의 경우는 Update() 함수 내에서 매 프레임마다 Key 입력을 전부
검사하기에 비효율적이라고 생각해서 후자를 사용하기로 했다.!

---
## Script

### InputManager.cs
``` C#
using System;
using System.Collections;
using System.Collections.Generic;
using System.Net;
using Unity.VisualScripting;
using UnityEngine;
public class InputManager 
{
    public Action KeyAction = null;
    public void OnUpdate()
    {
        if (Input.anyKey == false) return;
        if (KeyAction != null)
        {
            KeyAction.Invoke();
        }
    }
}

```
- OnUpdate()

     `if (Input.anyKey == false) return;`

    어떤 입력도 들어오지 않았다면 리턴한다.


---
### GameManager.cs
```C#
using Cinemachine;
using System.Collections;
using System.Collections.Generic;
using Unity.VisualScripting;
using UnityEditor.SceneManagement;
using UnityEngine;
using UnityEngine.SceneManagement;

public class GameManager : MonoBehaviour
{
    static GameManager GameMgr;
    public static GameManager Instance { get { Init(); return GameMgr; } }
    InputManager InputMgr = new InputManager();
    public static InputManager Input { get { return Instance.InputMgr; } }

    public Canvas canvas;
    bool bInteractingNpc;

    void Start()
    {
        Init();
    }

    void Update()
    {
        InputMgr.OnUpdate();
    }

    static void Init()
    {
        if (GameMgr != null)
        {
            GameObject obj = GameObject.Find("GameManger");
            if (obj == null)
            {
                obj = new GameObject { name = "GameManager" };
                obj.AddComponent<GameManager>();
            }

            DontDestroyOnLoad(obj); //씬 변경되도 오브젝트 유지
            GameMgr = obj.GetComponent<GameManager>();
        }
    }
}

```

- GameManager.cs 안에서 InputManager를 생성해 매 프레임마다 Update()가 호출될 때 InputMgr의 OnUpdate를 호출한다.

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

    void Start()
    {
        GameManager.Input.KeyAction += OnMouseClicked;
    }

 
    void Update()
    {
        
    }

    void OnMouseClicked()
    {
        
    }
}

```

- Start()

    `void Start()
    {
        GameManager.Input.KeyAction += OnMouseClicked;
    }
    `
    - InputManager의 KeyAction에 OnMouseClicked 함수를 구독시켰다. <br>
    = keyAction이 Null이 아니게 되어 OnUpdate()가 호출될 떄 OnMouseClicked가 실행된다.

---

마우스 입력이 가능해졌으니 Npc와의 상호작용 기능을 만들어 볼 것이다!
