---
title: "Сравнение буферов"
description: "В этом примере кода показано, как сравнивать буферы в приложении универсальной платформы для Windows (UWP)."
ms.assetid: CB086E51-544A-470D-B7C8-C055271CD615
author: awkoren
translationtype: Human Translation
ms.sourcegitcommit: b41fc8994412490e37053d454929d2f7cc73b6ac
ms.openlocfilehash: fcbc3c8a3f70613635201732f3ca5053d58325c7

---

# Сравнение буферов


\[ Обновлено для приложений UWP в Windows 10. Статьи для Windows 8.x можно найти в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]

В этом примере кода показано, как сравнивать буферы в приложении универсальной платформы для Windows (UWP).

```cs
public void CompareBuffers()
{
    // Create a hexadecimal string.
    String strHex = "30310aff";

    // Create a Base64 string that is equivalent to strHex.
    String strBase64v1 = "MDEK/w==";

    // Create a Base64 string that is not equivalent to strHex.
    String strBase64v2 = "KEDM/w==";

    // Decode strHex to a buffer.
    IBuffer buff1 = CryptographicBuffer.DecodeFromHexString(strHex);

    // Decode strBase64v1 to a buffer.
    IBuffer buff2 = CryptographicBuffer.DecodeFromBase64String(strBase64v1);

    // Decode strBase64v2 to a buffer.
    IBuffer buff3 = CryptographicBuffer.DecodeFromBase64String(strBase64v2);

    // Compare the hexadecimal-decoded buff1 to the Base64-decoded buff2.
    // The code points in the two buffers are equal, and the Boolean value
    // is true.
    Boolean bVal_1 = CryptographicBuffer.Compare(buff1, buff2);

    // Compare the hexadecimal-decoded buff1 to the Base64-decoded buff3.
    // The code points in the two buffers are not equal, and the Boolean value
    // is false.
    Boolean bVal_2 = CryptographicBuffer.Compare(buff1, buff3);
}
```


<!--HONumber=Jun16_HO4-->


