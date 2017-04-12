---
title: "Развернуть профили сканер штрихкодов с помощью MDM"
author: mukin
description: "Профили сканера штрихкодов можно развернуть с помощью MDM-сервера."
ms.assetid: 99ED3BD8-022C-40C2-9C65-F599186548FE
ms.openlocfilehash: 51d3b90dd7f202fd86285bb3f95c78ded4a8b6d8
ms.sourcegitcommit: 909d859a0f11981a8d1beac0da35f779786a6889
translationtype: HT
---
# <a name="deploy-barcode-scanner-profiles-with-mdm"></a>Развернуть профили сканер штрихкодов с помощью MDM

**Примечание.** Для этой функции требуется Windows 10 Mobile или более поздней версии.

Профили сканера штрихкодов можно развернуть с помощью MDM-сервера. Чтобы развернуть профили, используйте *OemProfile* в [EnterpriseExtFileSystem CSP](https://msdn.microsoft.com/library/windows/hardware/mt157025) для размещения их в папке \\Data\\SharedData\\OEM\\Public\\Profile. Эти профили сканера могут затем использоваться производителями драйвера для настройки параметров, недоступных через поверхность API.

Майкрософт не определяет специфику профиля сканера или методы их реализации.

## <a name="related-topics"></a>Еще по теме
[Сканер штрихкодов](barcode-scanner.md)

[Поставщик служб конфигурации EnterpriseExtFileSystem](https://msdn.microsoft.com/library/windows/hardware/mt157025)