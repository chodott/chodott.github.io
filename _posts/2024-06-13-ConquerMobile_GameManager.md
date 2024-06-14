---
layout: post
title: 모바일 정복 프로젝트(1) - GameManager

caregories:
  - Unity
 
tags:
  - [BFS]

date: 2024-06-13
---

## GameManager
- 게임을 전반적으로 관리하는 스크립트
- 싱글톤 패턴

프로그램에 단 하나만 존재해야 한다. 또한 어떤 곳에서든 쉽게 접근이 가능해야 한다.

유니티를 처음 접하게 되면서 여러 블로그들을 참고하고 있는데, 대부분의 작품에서 게임 매니저를 만드는 것을 보았다.

현재 구상하고 있는 프로젝트는 여러 미니게임들을 플레이하면서 최고 기록을 달성하는 게임이다. <br>
따라서 여러 씬들을 오가며 체력과 점수 게임의 난이도를 저장해줄 오브젝트가 필요한데, <br>
이 녀석에게 맡기면 참 좋을 것 같아 우선적으로 작성했다.

### 스크립트

``` C#
using System.Collections;
using System.Collections.Generic;
using Unity.VisualScripting;
using UnityEngine;

public class GameManager : MonoBehaviour
{
    static GameManager GameMgr;
    public static GameManager Instance { Init(); get { return GameMgr; } }

    void Start()
    {
        Init();
    }

    void Update()
    {

    }

    static void Init()
    {
        if (GameMgr == null)
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
>`static GameManager GameMgr;`

- 여러 곳에서 접근 할 수 있도록 static으로 선언한다.

> `public static GameManager Instance { Init(); get { return GameMgr; } }`

- 프로퍼티를 사용해 get 접근자를 작성했다.

```c#
static void Init()
    {
        if (GameMgr == null)
        {

            GameObject obj = GameObject.Find("GameManager");
            if (obj == null)
            {
                obj = new GameObject { name = "GameManager" };
                obj.AddComponent<GameManager>();
            }

            DontDestroyOnLoad(obj); //씬 변경되도 오브젝트 유지
            GameMgr = obj.GetComponent<GameManager>();
        }
    }
```
- Init 함수를 통해 Instance가 이미 존재하면 리턴한다
- 없을 경우 GameManager라는 오브젝트를 찾는다. 없다면 직접 만든다.!

> `void Start()
    {
        Init();
    }`

- start함수에 Init을 호출하여 게임이 시작되면 GameManager가 존재하도록 한다.

