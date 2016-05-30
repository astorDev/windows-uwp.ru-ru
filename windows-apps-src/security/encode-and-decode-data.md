---
title: Кодирование и декодирование данных
description: В этом примере кода показано, как кодировать и декодировать данные base64 и шестнадцатеричные данные в приложении универсальной платформы Windows (UWP).
ms.assetid: 2CC23863-E840-48F4-B087-0479045743AC
author: awkoren
---

# Кодирование и декодирование данных


\[ Обновлено для приложений UWP в Windows 10. Статьи о Windows 8.x см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]

В этом примере кода показано, как кодировать и декодировать данные base64 и шестнадцатеричные данные в приложении универсальной платформы Windows (UWP).

```cs
public void EncodeDecodeBase64()
{
    // Define a Base64 encoded string.
    String strBase64 = "uiwyeroiugfyqcajkds897945234==";

    // Decoded the string from Base64 to binary.
    IBuffer buffer = CryptographicBuffer.DecodeFromBase64String(strBase64);

    // Encode the buffer back into a Base64 string.
    String strBase64New = CryptographicBuffer.EncodeToBase64String(buffer);
}

public void EncodeDecodeHex()
{
    // Define a hexadecimal string.
    String strHex = "30310AFF";

    // Decode a hexadecimal string to binary.
    IBuffer buffer = CryptographicBuffer.DecodeFromHexString(strHex);

    // Encode the buffer back into a hexadecimal string.
    String strHexNew = CryptographicBuffer.EncodeToHexString(buffer);
}
```


<!--HONumber=May16_HO2-->


