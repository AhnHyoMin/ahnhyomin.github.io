---
layout: post
type: post
title: "관찰자 패턴(Observer Pattern)"
date: 2018-12-03 15:24:00
published: false
comments: true
categories: [DesignPattern]
---

# <center> 관찰자 패턴(Observer Pattern)  </center>
  >객체 사이에 일 대 다의 의존 관계를 정의해두어, 어떤 객체의 상태가 변할 때 그 객체에 의존성을 가진 다른 객체들이 그 변화를 통지 받고 자동으로 업데이트될 수 있게 만듭니다.(GoF의 디자인 패턴 392p)

---

### 작성 계기
 - 이것도 마찬가지로 면접을 계기로 작성하게 된다. 사실상 지금까지 그냥 만들줄만 알고 다양한 패턴을 적당한 상황에 어떤식으로 효율적으로 적용하여 사용할 줄 몰랐다. 이러이러한 패턴이 있다는 것만 알았지 실제로 적용하여 사용조차 안했다는 것이다. 면접관분들이 "패턴 어떤거 사용해보셨나요?" 라고 물어봤을때 나는 대답할 수 있는게 없었다. 그래서 지식의 시야를 넓히기 위해 알게 모르게 쓰던 패턴들을 이해하기 위해 작성을하게된다.

---
### 구현 이론
 - **핵심** : 여러 자료를 보고 개인적으로 생각하는  관찰자 패턴이 큰 특성(핵심)은 기능의 분리 라고 본다.기능의 분리는 즉 코드간의 결합도를 낮추는것으로 Decoupling(어떤 코드를 수정했을 때 다른 코드를 거의 바꾸지 않아도 됨)을 잘하게 만들어 주기때문에 작업 분담에도 매우 유용하다.
 - 시스템의 특성을 본다면 크게 3가지로 나눠진다. 기능을 관리하는 관리자, 특정한 일을 하는 기능, 기능을 호출하고자 하는 대상  
 - **특성 3가지** : 관리자, 기능, 대상


### 참고 이미지  
<Center><img src='{{ "/assets/post/desigepatterns/observerpattern/observerpattern_01.jpg" | absolute_url }}'  width="100%" height="100%"></Center>

---
### 코드 구현
  - 다음은 예제는 두 오브젝트가 충돌하면 CSubject 스크립트가 달린 오브젝트에서 업적 달성 로그를 띄우는 간단한 예제이다. 그리고 내 코드스타일과 특성 3가지에 따른 코드 구현이다.


###### CAchievementManager.cs
- 빈 오브젝트 생성 - > 스크립트 추가  

```c++
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

// 업적 키
public enum E_AchievementKey
{
    NONE,
    ON_COLLISION_ENTER,
    ON_COLLISION_STAY,
    ON_COLLISION_EXIT,


}

public class CAchievementManager : MonoBehaviour
{

    // 업적정보 풀
   private Dictionary<E_AchievementKey, CAchievement> m_AchievementKey = new Dictionary<E_AchievementKey, CAchievement>();

    // 전역으로 접근하기 위한 인스턴스
     private static CAchievementManager m_Instance;

    // 인스턴스 접큰 프로퍼티
    public static CAchievementManager Get
    {
        get
        {
            return m_Instance;
        }
    }

    private void Awake()
    {
        m_Instance = this;
    }

    private void Start()
    {
        //임시로 생성

        CAchievement _Achievement = new CAchievement();
        AddAchievement(E_AchievementKey.ON_COLLISION_ENTER , _Achievement);
         _Achievement = new CAchievement();
        AddAchievement(E_AchievementKey.ON_COLLISION_STAY , _Achievement);
         _Achievement = new CAchievement();
        AddAchievement(E_AchievementKey.ON_COLLISION_EXIT , _Achievement);

    }

    // 업적 추가하기
    public void AddAchievement(E_AchievementKey _Key, CAchievement _Achievement)
    {
        if(m_AchievementKey.ContainsKey(_Key) == false)
        {
            // 업적 정보 키가 없을경우 추가
            m_AchievementKey.Add(_Key , _Achievement);
        }
        else
        {
            //  예외처리
            Debug.Log(_Key.ToString() + " is Key In Dictionary");
        }
    }

    // 업적 지우기
    public void RemoveAchievement(E_AchievementKey _Key , CAchievement _Achievement)
    {
        if (m_AchievementKey.ContainsKey(_Key) == true)
        {
            // 업적 정보키가  있다면 삭제
            m_AchievementKey.Remove(_Key);
        }
        else
        {
            //  예외처리
            Debug.Log(_Key.ToString() + " is NotFound");
        }
    }

    // 업적 언락 하기
    public void OnUnlock(E_AchievementKey _Key)
    {
        // 언락되었는지 체크
        if (IsKeyUnLock(_Key) == true)
            return;

        if(m_AchievementKey.ContainsKey(_Key) == true)
        {
            // 업적 정보 키가 존재하면 업적 달성 호출
            m_AchievementKey[_Key].OnNotify(_Key);
        }
        else
        {
            //  예외처리
            Debug.Log(_Key.ToString() + " is NotFound");
        }
    }

    // 업적이 언락 되었는지
    public bool IsKeyUnLock(E_AchievementKey _Key)
    {
        if (m_AchievementKey.ContainsKey(_Key) == true)
        {
            // 업적 정보 키가 존재하면 업적 달성 호출
            return m_AchievementKey[_Key].IsUnLock;
        }
        else
        {
            //  예외처리
            Debug.Log(_Key.ToString() + " is NotFound");

            return true;
        }
    }
}

```

###### CAchievement.cs  
```c++
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public  class CAchievement
{
    // 업적 언락 상태
    bool m_IsUnLock = false;

    public bool IsUnLock
    {
        get
        {
            return m_IsUnLock;
        }

        set
        {
            m_IsUnLock = value;
        }
    }


    //업적 언락 로그 띄우기
    public  void OnNotify(E_AchievementKey _E_AchievementKey)
    {
        switch (_E_AchievementKey)
        {
            case E_AchievementKey.NONE:
                break;
            case E_AchievementKey.ON_COLLISION_ENTER:
                Debug.Log("ON_COLLISION_ENTER 달성");
                m_IsUnLock = true;
                break;
            case E_AchievementKey.ON_COLLISION_STAY:
                Debug.Log("ON_COLLISION_STAY 달성");
                m_IsUnLock = true;
                break;
            case E_AchievementKey.ON_COLLISION_EXIT:
                Debug.Log("ON_COLLISION_EXIT 달성");
                m_IsUnLock = true;
                break;
            default:
                break;
        }
    }
}


```

###### CSubject.cs
- 오브젝트 추가 -> 스크립트 추가

```c++
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class CSubject : MonoBehaviour {

	// Use this for initialization
	void Start () {

	}

	// Update is called once per frame
	void Update () {

	}

    private void OnTriggerEnter(Collider other)
    {
        CAchievementManager.Get.OnUnlock(E_AchievementKey.ON_COLLISION_ENTER);
    }

    private void OnTriggerStay(Collider other)
    {
        CAchievementManager.Get.OnUnlock(E_AchievementKey.ON_COLLISION_STAY);
    }

    private void OnTriggerExit(Collider other)
    {
        CAchievementManager.Get.OnUnlock(E_AchievementKey.ON_COLLISION_EXIT);
    }

}

```

---
### 마무리
- 아래 이미지를 보면 두 오브젝트가 충돌하자 각 키에대한 업적이 달성되었다는 로그가 출력되었다.
- 각각의 기능을 분리함으로써 코드가 깔끔해지고 서로의 기능에 간섭또한 없다. 코드 수정도 편해지고 분담 작업시 서로의 작업에 큰 영향을 주지 않는다.
- 내가 생각하는 관찰자 패턴과 실제 관찰자 패턴이 다를 수 있다. 내가 잘못 이해하고 있울 수도 있다. 그러니 그냥 이런게 있구나 참고 정도만 했으면 한다. 아니면 피드백좀...
<Center><img src='{{ "/assets/post/desigepatterns/observerpattern/observerpattern_02.jpg" | absolute_url }}'  width="100%" height="100%"></Center>
