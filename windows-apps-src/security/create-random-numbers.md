---
title: Создание случайных значений
description: В этом примере кода показано, как создать случайное число или буфер для использования в шифровании в приложении универсальной платформы Windows (UWP).
ms.assetid: 15746824-F93A-4DC7-836E-EBA916D2CFD3
author: msatranjr
ms.author: misatran
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: Windows 10, uwp, безопасности
ms.localizationpriority: medium
ms.openlocfilehash: 595b4ab47e3c6c833a4b8f2e692a0cc0c8ffcaa4
ms.sourcegitcommit: 9e2c34a5ed3134aeca7eb9490f05b20eb9a3e5df
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/17/2018
ms.locfileid: "3989163"
---
# <a name="create-random-numbers"></a>Создание случайных чисел



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