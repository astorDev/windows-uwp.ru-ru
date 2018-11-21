---
title: Копирование в массивы байтов и из них
description: В этом примере кода показано, как копировать в массивы байтов и из них в приложении универсальной платформы Windows (UWP).
ms.assetid: C343B08C-1FA1-40FD-8CA5-7FC9B707C5E3
author: msatranjr
ms.author: misatran
ms.date: 02/08/2017
ms.topic: article
keywords: Windows 10, uwp, безопасности
ms.localizationpriority: medium
ms.openlocfilehash: 22ce577f7d70b3750a365462b64181a27e71428e
ms.sourcegitcommit: 93c0a60cf531c7d9fe7b00e7cf78df86906f9d6e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/21/2018
ms.locfileid: "7558153"
---
# <a name="copy-to-and-from-byte-arrays"></a>Копирование в массивы байтов и из них



В этом примере кода показано, как копировать в массивы байтов и из них в приложении универсальной платформы Windows (UWP).

```cs
public void ByteArrayCopy()
{
    // Initialize a byte array.
    byte[] bytes = { 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10 };

    // Create a buffer from the byte array.
    IBuffer buffer = CryptographicBuffer.CreateFromByteArray(bytes);

    // Encode the buffer into a hexadecimal string (for display);
    string hex = CryptographicBuffer.EncodeToHexString(buffer);

    // Copy the buffer back into a new byte array.
    byte[] newByteArray;
    CryptographicBuffer.CopyToByteArray(buffer, out newByteArray);
}
```