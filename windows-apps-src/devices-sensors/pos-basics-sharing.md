---
author: TerryWarwick
title: Совместное использование устройств PointOfService
description: Совместное использование периферийные устройства PointOfService с другими
ms.author: jken
ms.date: 06/14/2018
ms.topic: article
keywords: Windows 10, UWP, точка обслуживания, POS
ms.localizationpriority: medium
ms.openlocfilehash: 591ba592d1c17b03ae29c6fb98ef546bc18b8bdc
ms.sourcegitcommit: ed0304b8a214c03b8aab74b8ef12c9f82b8e3c5f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/19/2018
ms.locfileid: "7290442"
---
# <a name="pointofservice-device-sharing"></a>Совместное использование устройств PointOfService

Узнайте, как совместного доступа сети или периферийных устройств Bluetooth подключены другие компьютеры в среде, где компьютерах полагаться на периферийных устройств, а не выделенный периферийные устройства, подключенные к каждому компьютеру.

## <a name="device-sharing"></a>Устройства общего доступа

Сеть и Bluetooth, подключенные периферийные устройства PointOfService обычно используются в среде wheere несколько клиентских устройств совместно используют же периферийные устройства во время дня.  В среде занят retail или служб еда за любой задержки возможность клиентскому устройству для присоединения к периферийного устройства влияет на эффективность, в котором можно закрыть транзакций с клиентом и перейти к следующей коллеге. В сценарии ресторана быстрого службы, где принтер квитанций используется в качестве принтера кухню для передачи сведений о заказ клиента на кухне для подготовки будет несколько клиентских устройств перевод заказы от клиентов.  После завершения порядок каждом клиентском устройстве сможете для присвоения общего принтера и сразу же порядок для кухню.

В этих средах очень важно для приложения, чтобы полностью **Удалить** объект устройства, чтобы другой можно присвоить одного устройства.

Ликвидировать PosPrinter в конце блок «using»

```Csharp 
using Windows.Devices.PointOfService;
using(PosPrinter printer = await PosPrinter.FromIdAsync("Device ID"))
{
    if (printer != null)
    {
        // Exercise the printer.
    }

    // When leaving this scope, printer.Dispose() is automatically invoked, 
    // releasing the session we have with the printer.
}
```


Ликвидировать PosPrinter, явно вызвав метод Dispose()

```Csharp 
using Windows.Devices.PointOfService;

PosPrinter printer = await PosPrinter.FromIdAsync("Device ID");
if (printer != null)
{
    // Exercise the printer, then dispose of the printer explicitly.
    printer.Dispose();
}
```

## <a name="api-methods-used"></a>Методы API, используемые 

+ [BarcodeScanner.Dispose](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodescanner.dispose) 
+ [CashDrawer.Dispose](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.cashdrawer.dispose) 
+ [LineDisplay.Dispose](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.linedisplay.dispose) 
+ [MagneticStripeReader.Dispose](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.magneticstripereader.dispose)  
+ [PosPrinter.Dispose](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.posprinter.dispose) 


[!INCLUDE [feedback](./includes/pos-feedback.md)]
