---
layout: post
type: post
title: "터렛 회전"
date: 2018-11-25 14:00:00
published: true
comments: true
categories: [GameSystem]
---

# <center> 터렛 회전  </center>

---
### 터렛 회전 목표
  - 부모 오브젝트가 어떠한 방향으로 회전 하든 타겟의 좌표를 정확히 구해야함

### 핵심 구현방법
  - 타겟의 좌표, 위치등의 정보를 자신의 로컬 기준계산 한다.
    - tranform가 포함하고 있는 함수중 Inverse로 시작 하는 함수들이 로컬 기준으로 계산해준다.
    - InverseTransformDirection, InverseTransformVector, InverseTransformPoint

---
### 몸통 회전

```c++

// 몸통 좌우 회전
    protected void StraightRotateBody()
    {
        if (m_Target != null)
        {
            // 타겟의 방향을 월드 좌표기준으로 계산함
            Vector3 _TargetDir = m_Target.position - m_Turretbody.position;

            //타겟의 방향을 터렛의 로컬로 가져옴
            _TargetDir = m_TransformCache.InverseTransformDirection(_TargetDir);

            // 좌우 회전 값만 필요하기 때문에 y값은 0으로
            _TargetDir.y = 0;

            if (_TargetDir.normalized.Equals(Vector3.zero))
                return;

            if (m_LimitLeftAndRightAngle == true)
            {
                // 회전 각도를 제한 할경우

                // 좌우 방향에 따른 회전 제한각 설정
                if (_TargetDir.x >= 0.0f)
                {
                    _TargetDir = Vector3.RotateTowards(Vector3.forward , _TargetDir , Mathf.Deg2Rad * m_RightAngle , float.MaxValue);
                }
                else
                {
                    _TargetDir = Vector3.RotateTowards(Vector3.forward , _TargetDir , Mathf.Deg2Rad * m_LeftAngle , float.MaxValue);
                }
            }

            // 새로운 회전값을 만듦
            NewRotate(_TargetDir , m_Turretbody);

        }
        else
        {
            // 새로운 회전값을 만듦
            NewRotate(Vector3.zero , m_Turretbody);

        }
    }

```


---
### 포신 회전


```c++

// 포신 상하 회전
    protected void StraightRotateBarral()
    {
      // 몸통 좌우 회전할때 barral이 이상하게 꼬이는 현상이 있어서
      // NewRotate함수를 사용하지 않고 오일러각을 구해서 회전시킨다.



        if (m_Target != null)
        {
            ////     타겟의 월드 좌표를 로컬 좌표로 변환
            Vector3 _TargetDir = m_Target.position - m_Turretbody.position;

            //타겟의 방향을 터렛의 로컬로 가져옴
            _TargetDir = m_Turretbody.InverseTransformDirection(_TargetDir);

            if (_TargetDir.normalized.Equals(Vector3.zero))
                return;

            // 회전생성
            Quaternion _LookRotation = Quaternion.LookRotation(_TargetDir*Time.deltaTime, Vector3.up);


            // 오일러 생성
            Quaternion _Euler = Quaternion.Euler(_LookRotation.eulerAngles.x, 0, 0);

            // 최종 회전
            m_TurretBarral.localRotation = Quaternion.RotateTowards(m_TurretBarral.localRotation , _Euler , m_RotationBarralSpeed * Time.deltaTime);

        }
        else
        {
            NewRotate(Vector3.zero , m_TurretBarral);

        }

    }

```

---
### 회전 처리

```c++
    // 새로운 회전 생성
    protected void NewRotate(Vector3 _LocalTarget, Transform _Turret)
    {
        Quaternion _RotationGoal = Quaternion.identity;
        if (_LocalTarget != Vector3.zero)
        {
            // 타겟을 바라보는 회전각 생성
            _RotationGoal = Quaternion.FromToRotation(Vector3.forward,_LocalTarget);
        }

        // 새로운 회전각 생성
        Quaternion _NewRotation = Quaternion.RotateTowards(_Turret.localRotation, _RotationGoal, m_RotationBarralSpeed * Time.deltaTime);

        // 타겟의 방향으로 회전
        _Turret.localRotation = _NewRotation;
    }

```
