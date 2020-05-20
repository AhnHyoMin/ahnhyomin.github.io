---
layout: post
type: post
title: "Unity IAP UnityEngine.VR.VRSettings::get_enabled() Exception"
date: 2020-05-19 13:00:00
published: true
comments: true
categories: [Android]
---

# <center> 에러 내용  </center>     

  -  Unity IAP 적용후 다음과 같은 에러 발생    
  > Error Unity Exception: Error: called non-existent method System.Boolean UnityEngine.VR.VRSettings::get_enabled()

---  

- Unity 버전 : 2019.3.13f
- Unity IAP 버전 : Unity IAP 1.23.1

---  

# <center> 원인 </center>
 - 최근 유니티 버전이 올라감에 따라  Namespace 이름이 다음과 같이 변경 되었다. UnityEngine.XR ->  UnityEngine.VR 이러한 이유로 Unity IAP package를 import후에 API가 업데이트 되지 않아 발생한 문제다

# <center> 해결 방법</center>
 - 본인은 다음과 같은 방법을 통해 해당 문제를 해결함

![image info](/assets/post/android/unityiap1.jpg)
 - plugins->Unitypurchasing-reimport
 - 해당 폴더 에셋파을들을 리임포트 해준다.

![image info](/assets/post/unity/apiupdatepopup1.jpg)
- 리임포트 후에 API update 팝업이 나오면 Go Ahead 해주자.
- 만약 해당 팝업이 안나온다면 Assets -> Run API update 해주자

![image info](/assets/post/unity/menualapiupdate1.jpg)
-  API Update 해준다음 빌드후 디버그 확인해보면 해당 에러가 더 이상 발생하지 않는다.
