---
title: Создание случайных значений
description: В этом примере кода показано, как создать случайное число или буфер для использования в шифровании в приложении универсальной платформы Windows (UWP).
ms.assetid: 15746824-F93A-4DC7-836E-EBA916D2CFD3
ms.date: 02/08/2017
ms.topic: article
keywords: Windows 10, uwp, безопасность
ms.localizationpriority: medium
ms.openlocfilehash: f36e50f2de36df39177370d688b9add5591403e1
ms.sourcegitcommit: 8921a9cc0dd3e5665345ae8eca7ab7aeb83ccc6f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/11/2018
ms.locfileid: "8895804"
---
# <a name="create-random-numbers"></a>Создание случайных значений



В этом примере кода показано, как создать случайное число или буфер для использования в шифровании в приложении универсальной платформы Windows (UWP).

```cs
public string GenerateRandomData()
{
    // Define the length, in bytes, of the buffer.
    uint length = 32;

    // Generate random data and copy it to a buffer.
    IBuffer buffer = CryptographicBuffer.GenerateRandom(length);

    // Encode the buffer to a hexadecimal string (for display).
    string randomHex = CryptographicBuffer.EncodeToHexString(buffer);

    return randomHex;
}

public uint GenerateRandomNumber()
{
    // Generate a random number.
    uint random = CryptographicBuffer.GenerateRandomNumber();
    return random;
}
```