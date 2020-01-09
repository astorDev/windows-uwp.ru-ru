---
title: Развернуть профили сканер штрихкодов с помощью MDM
description: Профили сканера штрихкодов можно развернуть с помощью MDM-сервера.
ms.assetid: 99ED3BD8-022C-40C2-9C65-F599186548FE
ms.date: 09/26/2017
ms.topic: article
keywords: Windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: f537833385582678b215804cac9a16002618c7e4
ms.sourcegitcommit: 26bb75084b9d2d2b4a76d4aa131066e8da716679
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/06/2020
ms.locfileid: "75684824"
---
# <a name="deploy-barcode-scanner-profiles-with-mdm"></a>Развернуть профили сканер штрихкодов с помощью MDM

**Обратите внимание** ,  для этого компонента требуется Windows 10 Mobile или более поздняя версия.

Профили сканера штрихкодов можно развернуть с помощью MDM-сервера. Чтобы развернуть профили, используйте *оемпрофиле* в [CSP ентерприсикстфилесистем](https://docs.microsoft.com/windows/client-management/mdm/enterpriseextfilessystem-csp) , чтобы поместить их в папку \\Data\\Шареддата\\OEM\\Public\\профиля. Эти профили сканера могут затем использоваться производителями драйвера для настройки параметров, недоступных через поверхность API.

Майкрософт не определяет специфику профиля сканера или методы их реализации.

## <a name="related-topics"></a>Связанные темы
- [CSP Ентерприсикстфилесистем](https://docs.microsoft.com/windows/client-management/mdm/enterpriseextfilessystem-csp)
- [Поддержка устройства сканера штрихкодов](https://docs.microsoft.com/windows/uwp/devices-sensors/pos-device-support#barcode-scanner)