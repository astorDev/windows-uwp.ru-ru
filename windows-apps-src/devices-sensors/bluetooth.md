---
ms.assetid: 404783BA-8859-4BFB-86E3-3DD2042E66F5
title: Bluetooth
description: Этот раздел содержит справочные статьи об интеграции Bluetooth в приложения универсальной платформы Windows (UWP), а также о том, как использовать RFCOMM, GATT и объявления о низком энергопотреблении (LE).
ms.date: 06/26/2020
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: aad368adf500c5968bf6374a5855a7e0dab0a044
ms.sourcegitcommit: 015291bdf2e7d67076c1c85fc025f49c840ba475
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/27/2020
ms.locfileid: "85469569"
---
# <a name="bluetooth"></a>Bluetooth
Этот раздел содержит справочные статьи о том, как интегрировать Bluetooth в приложения универсальной платформы Windows (UWP). Существует две разные технологии Bluetooth, которые можно реализовать в приложении.

> [!Important]
> В *Package. appxmanifest*необходимо объявить возможность "Bluetooth".
>
> `<Capabilities> <DeviceCapability Name="bluetooth" /> </Capabilities>`

## <a name="classic-bluetooth-rfcomm"></a>Классический Bluetooth (RFCOMM)
До появления Bluetooth с низким энергопотреблением устройства обычно использовали этот протокол для взаимодействия с помощью Bluetooth. Этот протокол является простым и удобным способом для взаимодействия между устройствами без необходимости экономии энергии. Дополнительные сведения об этом протоколе, включая примеры кода, см. в разделе [Bluetooth RFCOMM](send-or-receive-files-with-rfcomm.md).

## <a name="bluetooth-low-energy-le"></a>Bluetooth о низком энергопотреблении (LE)
Bluetooth с низким энергопотреблением (LE) — это спецификация, определяющая протоколы для обнаружения и обмена данными между устройствами, которые имеют требование к эффективному использованию энергии. Дополнительные сведения, включая примеры кода, см. в разделе [Bluetooth с низким энергопотреблением](bluetooth-low-energy-overview.md).

## <a name="see-also"></a>См. также
- [Вопросы и ответы для Bluetooth-разработчиков](bluetooth-dev-faq.md)