---
title: Совместное использование устройства PointOfService
description: Совместно с другими пользователями периферийные устройства PointOfService
ms.date: 06/14/2018
ms.topic: article
keywords: Windows 10, UWP, точка обслуживания, POS
ms.localizationpriority: medium
ms.openlocfilehash: 53dc22b2aa35b5e69854f6fb489ff6a454c73bf6
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57618949"
---
# <a name="pointofservice-device-sharing"></a>Совместное использование устройства PointOfService

Узнайте, как доступ сети или периферийные устройства Bluetooth подключен с другими компьютерами в среде, где нескольких ПК зависят от периферийных устройств, а не выделенные периферийные устройства, подключенные к каждому компьютеру.

## <a name="device-sharing"></a>Совместное использование устройств

Сеть и Bluetooth, подключенные периферийные устройства PointOfService обычно используются в среде wheere несколько клиентских устройств совместно используют же периферийные устройства, на протяжении всего дня.  В средах с высокой нагрузкой розничной продаже или питания задержек в возможности клиентских устройств для присоединения к периферийных устройств оказывает влияние на эффективность, в котором можно закрыть транзакцию с клиента и перейти к следующему коллеге. В сценарии ресторана быстрого службы, где принтер приемки используется как принтер кухни передавать сведения о заказе клиента на кухне для подготовки будет несколько клиентских устройств, которые принимает заказы от клиентов.  По завершении порядок каждого клиентского устройства должны иметь возможность утверждения общий принтер и немедленно печать заказа, полезные для Хозяйки возможности.

В этих средах, очень важно для приложения, чтобы полностью **dispose** устройство объект так, чтобы другой может запросить то же устройство.

Освобождение PosPrinter в конце блока «using»

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


Освобождение PosPrinter путем явного вызова Dispose()

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
