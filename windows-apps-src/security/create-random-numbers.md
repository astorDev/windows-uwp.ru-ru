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
keywords: Windows 10, uwp, безопасность
ms.localizationpriority: medium
ms.openlocfilehash: 595b4ab47e3c6c833a4b8f2e692a0cc0c8ffcaa4
ms.sourcegitcommit: 72710baeee8c898b5ab77ceb66d884eaa9db4cb8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/11/2018
ms.locfileid: "3847128"
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