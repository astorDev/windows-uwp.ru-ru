---
title: Преобразования между строками и двоичными данными
description: В этом примере кода показано, как выполнять преобразование между строками и двоичными данными в приложении универсальной платформы Windows (UWP).
ms.assetid: AED4C74F-E63B-4980-BB4D-28ACCC1AB58B
author: awkoren
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

<!--HONumber=May16_HO2-->


