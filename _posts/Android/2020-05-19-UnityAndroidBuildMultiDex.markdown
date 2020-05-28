---
layout: post
type: post
title: "유니티에서 Gradle 빌드, Multidex  사용하기"
date: 2020-05-18 13:00:00
published: true
comments: true
categories: [Android]
---

# <center> Gradle  </center>
  > Gradle은 Groovy를 이용한 빌드 자동화 시스템이다. Groovy와 유사한 도메인 언어를 채용하였으며, 현재 안드로이드 앱을 만드는데 필요한 안드로이드 스튜디오의 공식 빌드 시스템이기도 하다. Java, C/C++, 파이썬 등과 같은 여러 가지 언어를 지원한다. (**위키 백과**)

  > Gradle은 빌드 프로세스 수를 자동화하는 Android 빌드 시스템입니다. 이 자동화로 인해 가장 일반적인 빌드 오류가 발생할 확률이 줄어듭니다. 특히 Unity 에디터에서 Gradle을 사용하면 DEX(Dalvik Executable 포맷) 파일의 메서드 레퍼런스 수가 감소하므로 DEX 제한과 관련된 문제가 발생할 가능성이 더 적습니다. 하지만 Gradle과 디폴트 Unity Android 빌드 시스템의 차이점으로 인해 일부 기존 프로젝트를 Gradle로 전환하기 어려울 수 있습니다. (**Unity Documentation**)
# <center> Multidex  </center>
  >앱 및 앱이 참조하는 라이브러리에서 메서드가 65,536개를 초과하면 앱이 Android 빌드 아키텍처의 제한에 도달했음을 알리는 빌드 오류가 발생합니다.

---
- Unity 버전 : 2019.3.13f
- SDK 환경 : Google Play(GooglePlayGamesPlugin-0.10.09), 외 FireBase 3종


Project Setting -> Player -> Publishing -> Custom Gradle Template,Custom Launcher Gradle Template 활성화

---

# <center> AndroidX 사용 환경</center>

**Custom Gradle Template**  
- 다음 파일에 아래와 같은 코드가 있는경우 참고
```java
([rootProject] + (rootProject.subprojects as List)).each {
    ext {
        it.setProperty("android.useAndroidX", true)
        it.setProperty("android.enableJetifier", true)
    }
}
```

**Custom Launcher Gradle Template**  
- 추석 밑부분 코드 추가   

```Java
dependencies {
    // 추가 : 커스텀 추가
	 implementation 'androidx.multidex:multidex:2.0.1'
}

defaultConfig {
       minSdkVersion **MINSDKVERSION**
       targetSdkVersion **TARGETSDKVERSION**

     // 추가 : 멀티 덱스 on
      multiDexEnabled true

       applicationId '**APPLICATIONID**'
       ndk {
           abiFilters **ABIFILTERS**
       }
       versionCode **VERSIONCODE**
       versionName '**VERSIONNAME**'
   }

```

---

# <center> Android 사용 환경</center>

**Custom Gradle Template**  
 - 다음 파일에 아래와 같은 코드가 없는 경우(?), 또는 useAndroidX를 사용하지 않는 환경
```java
([rootProject] + (rootProject.subprojects as List)).each {
    ext {
        it.setProperty("android.useAndroidX", true)
        it.setProperty("android.enableJetifier", true)
    }
}
```

**Custom Launcher Gradle Template**  
- 추석 밑부분 코드 추가   

```java
dependencies {
  // 추가 : 커스텀 추가
  // multidex:1.0.3 이 부분은 사용 환경에 맞는 버전 사용
  implementation 'com.android.support:multidex:1.0.3'
}

defaultConfig {
       minSdkVersion **MINSDKVERSION**
       targetSdkVersion **TARGETSDKVERSION**

     // 추가 : 멀티 덱스 on
      multiDexEnabled true

       applicationId '**APPLICATIONID**'
       ndk {
           abiFilters **ABIFILTERS**
       }
       versionCode **VERSIONCODE**
       versionName '**VERSIONNAME**'
   }

```
