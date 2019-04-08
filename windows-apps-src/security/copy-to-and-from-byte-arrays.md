---
title: Копирование в массивы байтов и из них
description: В этом примере кода показано, как копировать в массивы байтов и из них в приложении универсальной платформы Windows (UWP).
ms.assetid: C343B08C-1FA1-40FD-8CA5-7FC9B707C5E3
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp, security
ms.localizationpriority: medium
ms.openlocfilehash: b3ce63ca1780f9ed1ecd32f3ab1c029a1a92e1b5
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57608809"
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