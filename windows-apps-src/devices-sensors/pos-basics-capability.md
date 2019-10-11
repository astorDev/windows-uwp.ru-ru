---
title: Возможность устройств PointOfService
description: Возможность PointOfService требуется для использования пространства имен Windows.Devices.PointOfService.
ms.date: 05/02/2018
ms.topic: article
keywords: Windows 10, UWP, точка обслуживания, POS
ms.localizationpriority: medium
ms.openlocfilehash: f120e093ab65224ca4c32b64640100b6abd1a36a
ms.sourcegitcommit: 0301f794f994e604ffc131de7e40ffcede3530c9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/08/2019
ms.locfileid: "72036260"
---
# <a name="pointofservice-device-capability"></a>Возможность устройств PointOfService
Доступ к API-интерфейсам PointOfService запрашивается путем объявления возможности в манифесте пакета приложения. Большую часть возможностей можно объявить, используя конструктор манифестов в Microsoft Visual Studio, либо добавить их вручную.  

> [!Important]
> При попытке использовать API из пространства имен Windows. Devices. PointOfService вы получите ошибку **System. UnauthorizedAccessException** , если вы не объявили возможность **PointOfService** в манифесте приложения. 

## <a name="declare-capability-using-manifest-designer"></a>Объявите возможность с помощью конструктора манифестов

1. В **обозревателе решений** разверните узел проекта приложения UWP.
2. Дважды щелкните файл **Package.appxmanifest**.  
*Если файл манифеста уже открыт в представлении кода XML, Visual Studio предложит закрыть файл.*
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
