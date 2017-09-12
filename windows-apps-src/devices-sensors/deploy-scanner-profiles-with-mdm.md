---
title: "Развернуть профили сканер штрихкодов с помощью MDM"
author: PatrickFarley
description: "Профили сканера штрихкодов можно развернуть с помощью MDM-сервера."
ms.assetid: 99ED3BD8-022C-40C2-9C65-F599186548FE
ms.openlocfilehash: a63a09e64b6e2b935963a3f49ed7cbc6b82bdcef
ms.sourcegitcommit: d2ec178103f49b198da2ee486f1681e38dcc8e7b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/28/2017
---
# <a name="deploy-barcode-scanner-profiles-with-mdm"></a>Развернуть профили сканер штрихкодов с помощью MDM

**Примечание.** Для этой функции требуется Windows 10 Mobile или более поздней версии.

Профили сканера штрихкодов можно развернуть с помощью MDM-сервера. Чтобы развернуть профили, используйте *OemProfile* в [EnterpriseExtFileSystem CSP](https://msdn.microsoft.com/library/windows/hardware/mt157025) для размещения их в папке \\Data\\SharedData\\OEM\\Public\\Profile. Эти профили сканера могут затем использоваться производителями драйвера для настройки параметров, недоступных через поверхность API.

Майкрософт не определяет специфику профиля сканера или методы их реализации.

## <a name="related-topics"></a>Еще по теме
[Сканер штрихкодов](barcode-scanner.md)

[Поставщик служб конфигурации EnterpriseExtFileSystem](https://msdn.microsoft.com/library/windows/hardware/mt157025)