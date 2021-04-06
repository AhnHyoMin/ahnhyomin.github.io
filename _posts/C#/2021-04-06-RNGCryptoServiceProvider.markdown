---
layout: post
type: post
title: "중복 없는 UID만들기"
date: 2021-04-06 11:38:00
published: true
comments: true
categories: [CShap]

---


# <center>  중복없는 UID 만들기 </center> #  
 - 테스트 결과 : Loop를 50,100만번 돌려서 Dictionary에 값 체크하며 넣은 결과 중복값 없이 UID String이 생성 되었다.

```cs
public static string GetUniqueId(int length = 8, string mask = "abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ1234567890")
{
    char[] chars = mask.ToCharArray();
    RNGCryptoServiceProvider crypto = new RNGCryptoServiceProvider();
    byte[] data = new byte[length];
    crypto.GetNonZeroBytes(data);
    StringBuilder result = new StringBuilder(length);
    foreach (byte b in data)
    {
        result.Append(chars[b % (chars.Length - 1)]);
    }
    return result.ToString();
}
```
