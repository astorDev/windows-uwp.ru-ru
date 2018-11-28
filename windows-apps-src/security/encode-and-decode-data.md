---
title: Кодирование и декодирование данных
description: В этом примере кода показано, как кодировать и декодировать данные base64 и шестнадцатеричные данные в приложении универсальной платформы Windows (UWP).
ms.assetid: 2CC23863-E840-48F4-B087-0479045743AC
ms.date: 02/08/2017
ms.topic: article
keywords: Windows 10, uwp, безопасность
ms.localizationpriority: medium
ms.openlocfilehash: 3c4e694dca3c84c7e94e513d8bb10a3f405bbc86
ms.sourcegitcommit: b11f305dbf7649c4b68550b666487c77ea30d98f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/28/2018
ms.locfileid: "7852917"
---
# <a name="encode-and-decode-data"></a>Кодирование и декодирование данных



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
