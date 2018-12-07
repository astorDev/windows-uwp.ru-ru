---
title: Развернуть профили сканер штрихкодов с помощью MDM
description: Профили сканера штрихкодов можно развернуть с помощью MDM-сервера.
ms.assetid: 99ED3BD8-022C-40C2-9C65-F599186548FE
ms.date: 09/26/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: dbcaa683e2c7a2bb18d88fcba03e10fa951d4459
ms.sourcegitcommit: a3dc929858415b933943bba5aa7487ffa721899f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/07/2018
ms.locfileid: "8787077"
---
# <a name="deploy-barcode-scanner-profiles-with-mdm"></a>Развернуть профили сканер штрихкодов с помощью MDM

**Примечание**этой функции требуется Windows10 Mobile или более поздней версии.

Профили сканера штрихкодов можно развернуть с помощью MDM-сервера. Чтобы развернуть профили, используйте *OemProfile* в [EnterpriseExtFileSystem CSP](https://msdn.microsoft.com/library/windows/hardware/mt157025) для размещения их в папке \\Data\\SharedData\\OEM\\Public\\Profile. Эти профили сканера могут затем использоваться производителями драйвера для настройки параметров, недоступных через поверхность API.

Майкрософт не определяет специфику профиля сканера или методы их реализации.

## <a name="related-topics"></a>Еще по теме
- [Поставщик служб конфигурации EnterpriseExtFileSystem](https://msdn.microsoft.com/library/windows/hardware/mt157025)
- [Поддержка устройств сканера штрихкодов](https://docs.microsoft.com/en-us/windows/uwp/devices-sensors/pos-device-support#barcode-scanner)