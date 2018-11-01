---
author: TerryWarwick
title: Возможность устройств PointOfService
description: Возможность PointOfService требуется для использования пространства имен Windows.Devices.PointOfService.
ms.author: jken
ms.date: 05/1/2018
ms.topic: article
keywords: Windows 10, UWP, точка обслуживания, POS
ms.localizationpriority: medium
ms.openlocfilehash: dbed55af1a9fa3df14f0a7e3e7c6d1f599b40fd3
ms.sourcegitcommit: cd00bb829306871e5103db481cf224ea7fb613f0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/31/2018
ms.locfileid: "5864176"
---
# <a name="pointofservice-device-capability"></a>Возможность устройств PointOfService
Доступ к API-интерфейсам PointOfService запрашивается путем объявления возможности в манифесте пакета приложения. Большую часть возможностей можно объявить, используя конструктор манифестов в Microsoft Visual Studio, либо добавить их вручную.  

> [!Important]
> При попытке использования API в пространстве имен Winodws.Devices.PointOfService без объявления в манифесте приложения возможности **pointOfService** отобразится ошибка **System.UnauthorizedAccessException**. 

## <a name="declare-capability-using-manifest-designer"></a>Объявите возможность с помощью конструктора манифестов

1. В **обозревателе решений** разверните узел проекта приложения UWP.
2. Дважды щелкните файл **Package.appxmanifest**.  
*Если файл манифеста уже открыт в представлении кода XML, Visual Studio предложит вам закрыть файл.*
3. Откройте вкладку **Возможности**.
4. Установите флажок рядом с пунктом **Point of Service** в списке возможностей, чтобы включить возможность точки обслуживания для устройства.


## <a name="declare-capability-manually"></a>Объявите возможность вручную

1. В **обозревателе решений** разверните узел проекта приложения UWP.
2. Щелкните правой кнопкой мыши файл **Package.appxmanifest** и выберите команду **Перейти к коду**.
3. Добавьте элемент PointOfService DeviceCapability в раздел "Возможности" манифеста приложения.  

```xml
  <Capabilities>
    <DeviceCapability Name="pointOfService" />
  </Capabilities>
   ```
