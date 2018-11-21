---
author: TerryWarwick
title: Начало работы со сканерами штрихкодов на базе камеры
description: Узнайте, как использовать камеру в качестве сканера штрихкодов.
ms.author: jken
ms.date: 05/1/2018
ms.topic: article
keywords: Windows 10, UWP, точка обслуживания, POS
ms.localizationpriority: medium
ms.openlocfilehash: 12aabff66fc116f510dced78aa56f3df5f84c850
ms.sourcegitcommit: cbe7cf620622a5e4df7414f9e38dfecec1cfca99
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2018
ms.locfileid: "7429214"
---
# <a name="getting-started-with-a-camera-barcode-scanner"></a>Начало работы со сканерами штрихкодов на базе камеры
## <a name="step-1-add-capability-declarations-to-your-app-manifest"></a>Шаг 1. Добавление объявлений возможностей в манифест приложения
1. В Microsoft Visual Studio откройте конструктор манифеста приложения, дважды щелкнув элемент **package.appxmanifest** в **Обозревателе решений**.
2. Перейдите на вкладку **Возможности**.
3. Установите флажки **Веб-камера** и **PointOfService**. 

>[!NOTE] 
> Возможность **Веб-камера** необходима, чтобы программный декодер получал кадры с камеры для декодирования, а также для обеспечения возможности предварительного просмотра из вашего приложения.

## <a name="step-2-add-using-directives"></a>Шаг 2. Добавление директив using

```Csharp
using Windows.Devices.Enumeration;
using Windows.Devices.PointOfService;
```
## <a name="step-3-define-your-device-selector"></a>Шаг 3. Определение средства выбора устройств

### **<a name="option-a-find-all-barcode-scanners"></a>Вариант А. Поиск всех сканеров штрихкодов**

```Csharp
string selector = BarcodeScanner.GetDeviceSelector();       
```

### **<a name="option-b-scoping-device-selector-to-connection-type"></a>Вариант Б. Ограничение области действия средства выбора по типу подключения**

```Csharp
string selector = BarcodeScanner.GetDeviceSelector(PosConnectionTypes.Local);
DeviceInformationCollection deviceCollection = await DeviceInformation.FindAllAsync(selector);
```

## <a name="step-4-enumerate-barcode-scanners"></a>Шаг 4. Перечисление сканеров штрихкодов
Если вы предполагаете, что список устройств не будет меняться в течение существования вашего приложения, вы можете перечислить снимок только один раз с помощью *FindAllAsync*, но если список сканеров штрихкодов может измениться, вместо этого следует использовать *DeviceWatcher*.  

> [!Important] 
> Использование GetDefaultAsync для перечисления устройств PointOfService может привести к непредсказуемому поведению, так как этот просто возвращает первое найденное в классе устройство, которое может меняться от сеанса к сеансу.

### **<a name="option-a-enumerate-a-snapshot-of-barcode-scanners"></a>Вариант А. Перечисление снимка сканеров штрихкодов**
```Csharp
DeviceInformationCollection deviceCollection = await DeviceInformation.FindAllAsync(selector);
```

> [!TIP]
> Подробнее об использовании *FindAllAsync* см. в статье [*Перечисление снимка устройств*](https://docs.microsoft.com/windows/uwp/devices-sensors/enumerate-devices#enumerate-a-snapshot-of-devices).

### **<a name="option-b-enumerate-and-watch-for-changes-in-available-barcode-scanners"></a>Вариант Б. Перечисление доступных сканеров штрихкодов и отслеживание изменений в них**
```Csharp
DeviceWatcher deviceWatcher = DeviceInformation.CreateWatcher(selector);

// TODO: Add Event Listeners and Handlers
```
> [!TIP]
> Подробнее: [*Перечисление и отслеживание устройств*](https://docs.microsoft.com/windows/uwp/devices-sensors/enumerate-devices#enumerate-and-watch-devices) и [*DeviceWatcher*](https://docs.microsoft.com/uwp/api/Windows.Devices.Enumeration.DeviceWatcher).

## <a name="step-5-identify-camera-barcode-scanners"></a>Шаг 5. Идентификация сканеров штрихкодов на базе камер
Сканер штрихкодов на базе камеры создается динамически, когда Windows связывает камеру или камеры, подключенные к компьютеру, с программным декодером.  Каждая пара "камера-декодер" представляет собой полнофункциональный сканер штрихкодов.

Для каждого сканера штрихкодов в полученной коллекции устройств можно определить, является ли оно сканером на базе камеры или физическим сканеров штрихкодов, путем проверки свойства [*BarcodeScanner.VideoDeviceID*](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodescanner.videodeviceid#Windows_Devices_PointOfService_BarcodeScanner_VideoDeviceId).  Если свойство VideoDeviceID не равно NULL, это значит, что объект сканера штрихкодов из коллекции устройств представляет собой сканер штрихкодов на базе камеры.  Если у вас несколько сканеров штрихкодов на базе камеры, имеет смысл создать отдельную коллекцию, куда не будут входить физические сканеры. 

Сканеры штрихкодов на базе камеры, которые используют встроенный в Windows декодер, будут отображаться следующим образом: 

> Microsoft BarcodeScanner (*название вашей камеры*)

Если это камера, встроенная в корпус компьютера, в названии может быть указано, *передняя* она или *задняя* (если камер две).  В будущем могут появиться дополнительные программные декодеры, из-за чего схема именования изменится.

## <a name="step-6-claim-the-camera-barcode-scanner"></a>Шаг 6. Присвоение сканера штрихкодов на базе камеры 
Используйте метод [BarcodeScanner.ClaimScannerAsync](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodescanner.claimscannerasync#Windows_Devices_PointOfService_BarcodeScanner_ClaimScannerAsync) для получения права на исключительное использование сканера штрихкодов на базе камеры.

## <a name="step-7-system-provided-preview"></a>Шаг 7: Предварительное изображение, предоставляемое системой
Предварительное изображение с камеры необходимо, чтобы пользователь мог успешно наводить камеру на штрихкоды.  Windows предоставляет простой механизм предварительного просмотра, который запускает диалоговое окно для простейшего управления сканером штрихкодов на базе камеры.  Просто вызовите метод [ClaimedBarcodeScanner.ShowVideoPreview](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.claimedbarcodescanner.showvideopreviewasync), чтобы открыть диалоговое окно, и метод [ClaimedBarcodeScanner.HideVideoPreview](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.claimedbarcodescanner.hidevideopreview), чтобы закрыть его после завершения.

> [!TIP]
> О том, как разместить в своем приложении предварительное изображение со сканера штрихкодов на базе камеры, см. в статье [Размещение предварительного изображения](pos-camerabarcode-hosting-preview.md).

## <a name="step-8-initiate-scan"></a>Шаг 8: Проверка инициирование 
Запускать процесс сканирования можно путем вызова метода [**StartSoftwareTriggerAsync**](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.claimedbarcodescanner.startsoftwaretriggerasync#Windows_Devices_PointOfService_ClaimedBarcodeScanner_StartSoftwareTriggerAsync).  
В зависимости от значения свойства [**IsDisabledOnDataReceived**](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.claimedbarcodescanner.isdisabledondatareceived#Windows_Devices_PointOfService_ClaimedBarcodeScanner_IsDisabledOnDataReceived) сканер может отсканировать только один штрихкод и остановиться или сканировать постоянно, пока не будет вызван метод [**StopSoftwareTriggerAsync**](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.claimedbarcodescanner.stopsoftwaretriggerasync#Windows_Devices_PointOfService_ClaimedBarcodeScanner_StopSoftwareTriggerAsync).

Задайте одно из значений [**IsDisabledOnDataReceived**](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.claimedbarcodescanner.isdisabledondatareceived#Windows_Devices_PointOfService_ClaimedBarcodeScanner_IsDisabledOnDataReceived), чтобы задать нужное поведение сканера при декодировании штрихкода.

| Значение | Описание |
| ----- | ----------- |
| True   | Сканировать только один штрихкод, затем остановиться |
| False  | Сканировать штрихкоды без остановки |