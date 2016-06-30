---
title: "Преобразования между строками и двоичными данными"
description: "В этом примере кода показано, как выполнять преобразование между строками и двоичными данными в приложении универсальной платформы Windows (UWP)."
ms.assetid: AED4C74F-E63B-4980-BB4D-28ACCC1AB58B
author: awkoren
translationtype: Human Translation
ms.sourcegitcommit: b41fc8994412490e37053d454929d2f7cc73b6ac
ms.openlocfilehash: 667338ed3808f79b75230bc0c3ea00cc7e274d5a

---

# Преобразование между строками и двоичными данными


\[ Обновлено для приложений UWP в Windows 10. Статьи, касающиеся Windows 8.x, см. в разделе [Архив](http://go.microsoft.com/fwlink/p/?linkid=619132) \]

В этом примере кода показано, как выполнять преобразование между строками и двоичными данными в приложении универсальной платформы Windows (UWP).

```cs
public void ConvertData()
{
    // Create a string to convert.
    String strIn = "Input String";

    // Convert the string to UTF16BE binary data.
    IBuffer buffUTF16BE = CryptographicBuffer.ConvertStringToBinary(strIn, BinaryStringEncoding.Utf16BE);

    // Convert the string to UTF16LE binary data.
    IBuffer buffUTF16LE = CryptographicBuffer.ConvertStringToBinary(strIn, BinaryStringEncoding.Utf16LE);

    // Convert the string to UTF8 binary data.
    IBuffer buffUTF8 = CryptographicBuffer.ConvertStringToBinary(strIn, BinaryStringEncoding.Utf8);
}
```


<!--HONumber=Jun16_HO4-->


