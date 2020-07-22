---
layout: post
type: post
title: "구글 스토어에서 앱버전 가져오기"
date: 2020-05-26 13:00:00
published: true
comments: true
categories: [CShap]

---


# <center> 구글 앱버전 파싱 코드</center> #  
 -  구글 플레이 스토어에서 앱버전 찾아서 검사한다.

```cs

public static IEnumerator AppVersionCheck(Action<bool> _State)
{
#if !UNITY_EDITOR && UNITY_ANDROID
    string _AppID = "https://play.google.com/store/apps/details?id=com.RaGames.EgyptMatchPop";
#elif UNITY_EDITOR
    string _AppID = "https://play.google.com/store/apps/details?id=com.RaGames.ZombieCrisis";
#endif

    UnityWebRequest _WebRequest = UnityWebRequest.Get(_AppID);

    yield return _WebRequest.SendWebRequest();

    // 정규식으로 전채 문자열중 버전 정보가 담겨진 태그를 검색한다.
    string _Pattern = @"<span class=""htlgb"">[0-9]{1,3}[.][0-9]{1,3}[.][0-9]{1,3}<";
    Regex _Regex = new Regex(_Pattern, RegexOptions.IgnoreCase);
    Match _Match = _Regex.Match(_WebRequest.downloadHandler.text);

    if (_Match != null)
    {
        // 버전 정보가 담겨진 태그를 찾음
        // 해당 태그에서 버전 넘버만 가져온다
        _Match = Regex.Match(_Match.Value, "[0-9]{1,3}[.][0-9]{1,3}[.][0-9]{1,3}");


        try
        {
            int[] _ClientVersion = VersionPaser(Application.version);
            int[] _AppStoreVersion = VersionPaser(_Match.Value);

            Debug.Log("  Application.version : " + Application.version + ", AppStore version :" + _Match.Value);

            if (_AppStoreVersion[0] != _ClientVersion[0] || _AppStoreVersion[1] != _ClientVersion[1] || _AppStoreVersion[2] != _ClientVersion[2])
            {
                if (_State != null)
                    _State(true);

                yield break;
            }
        }
        catch (Exception Ex)
        {
            // 비정상 버전정보 파싱중 Exception처리

            Debug.LogError("비정상 버전 정보 Exception : " + Ex);
            Debug.LogError("  Application.version : " + Application.version + ", AppStore version :" + _Match.Value);
        }


    }
    else
    {
        Debug.LogError("Not Found AppStoreVersion Info");
    }

    if (_State != null)
        _State(false);
}
```
