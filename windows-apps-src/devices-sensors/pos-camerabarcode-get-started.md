---
title: Начало работы со сканерами штрихкодов на базе камеры
description: Узнайте, как использовать камеру в качестве сканера штрихкодов.
ms.date: 09/02/2019
ms.topic: article
keywords: Windows 10, UWP, точка обслуживания, POS
ms.localizationpriority: medium
ms.openlocfilehash: b35ff6b183a6344fbc8da6b44a6cb81ea695a1c9
ms.sourcegitcommit: 0dec04de501a3db6b22dfd4a320fc09b5c4a21b5
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/04/2019
ms.locfileid: "70243292"
---
# <a name="getting-started-with-a-camera-barcode-scanner"></a>Начало работы со сканерами штрихкодов на базе камеры

Приведенные здесь фрагменты кода используются только в демонстрационных целях. Рабочий пример см. в разделе [Пример сканера штрихкодов](https://github.com/microsoft/Windows-universal-samples/tree/master/Samples/BarcodeScanner).

## <a name="step-1-add-capability-declarations-to-your-app-manifest"></a>Шаг 1. Добавление объявлений возможностей в манифест приложения

1. В Microsoft Visual Studio откройте конструктор манифеста приложения, дважды щелкнув элемент **package.appxmanifest**в **Обозревателе решений**.
2. Перейдите на вкладку **Возможности**.
3. Установите флажки **Веб-камера** и **PointOfService**.

>[!NOTE]
> Возможность **Веб-камера** необходима, чтобы программный декодер получал кадры с камеры для декодирования, а также для обеспечения возможности предварительного просмотра из вашего приложения.

## <a name="step-2-add-using-directives"></a>Шаг 2. Добавить директивы using

```Csharp
using Windows.Devices.Enumeration;
using Windows.Devices.PointOfService;
```

## <a name="step-3-define-your-device-selector"></a>Шаг 3. Определение селектора устройства

### <a name="option-a-find-all-barcode-scanners"></a>**Вариант а. Найти все сканеры штрихкодов**

```Csharp
string selector = BarcodeScanner.GetDeviceSelector();
```

### <a name="option-b-scoping-device-selector-to-connection-type"></a>**Вариант б. Определение области выбора устройства для типа подключения**

```Csharp
string selector = BarcodeScanner.GetDeviceSelector(PosConnectionTypes.Local);
DeviceInformationCollection deviceCollection = await DeviceInformation.FindAllAsync(selector);
```

## <a name="step-4-enumerate-all-barcode-scanners"></a>Шаг 4. Перечислить все сканеры штрихкодов

Если вы не планируете изменять список устройств в течение срока существования приложения, вы можете перечислить моментальный снимок только один раз с помощью [девицеинформатион. финдалласинк](https://docs.microsoft.com/uwp/api/windows.devices.enumeration.deviceinformation.findallasync), но если вы считаете, что список сканеров штрихкодов может измениться в течение срока существования приложение следует использовать вместо него [девицеватчер](https://docs.microsoft.com/uwp/api/windows.devices.enumeration.devicewatcher) .  

> [!Important]
> Использование GetDefaultAsync для перечисления устройств PointOfService может привести к непредсказуемому поведению, так как этот просто возвращает первое найденное в классе устройство, которое может меняться от сеанса к сеансу.

### <a name="option-a-enumerate-a-snapshot-of-barcode-scanners"></a>**Вариант а. Перечисление моментальных снимков сканеров штрихкодов**

```Csharp
DeviceInformationCollection deviceCollection = await DeviceInformation.FindAllAsync(selector);
```

> [!TIP]
> Подробнее об использовании *FindAllAsync* см. в статье [*Перечисление снимка устройств*](https://docs.microsoft.com/windows/uwp/devices-sensors/enumerate-devices#enumerate-a-snapshot-of-devices).

### <a name="option-b-enumerate-available-barcode-scanners-and-watch-for-changes-to-the-available-scanners"></a>**Вариант б. Перечисление доступных сканеров штрихкодов и просмотр изменений в доступных сканерах**

```Csharp
DeviceWatcher deviceWatcher = DeviceInformation.CreateWatcher(selector);
watcher.Added += Watcher_Added;
watcher.Removed += Watcher_Removed;
watcher.Updated += Watcher_Updated;
watcher.Start();
```

> [!TIP]
> Подробнее: [*Перечисление и отслеживание устройств*](https://docs.microsoft.com/windows/uwp/devices-sensors/enumerate-devices#enumerate-and-watch-devices) и [*DeviceWatcher*](https://docs.microsoft.com/uwp/api/Windows.Devices.Enumeration.DeviceWatcher).

## <a name="step-5-identify-camera-barcode-scanners"></a>Шаг 5. Обнаружение сканеров штрихкодов камеры

Сканер штрихкодов на базе камеры создается динамически, когда Windows связывает камеру или камеры, подключенные к компьютеру, с программным декодером.  Каждая пара "камера-декодер" представляет собой полнофункциональный сканер штрихкодов.

Для каждого сканера штрихкодов в результирующей коллекции устройств можно различать сканеры штрихкодов камеры и физические сканеры штрихкодов, проверив свойство [*баркодесканнер. видеодевицеид*](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodescanner.videodeviceid#Windows_Devices_PointOfService_BarcodeScanner_VideoDeviceId) .  Значение Видеодевицеид, отличное от NULL, указывает, что объект сканера штрихкода из коллекции устройств является сканером штрихкодов камеры.  При наличии нескольких сканеров штрихкодов камеры может потребоваться создать отдельную коллекцию, которая исключает физические сканеры штрихкодов.

Сканеры штрихкодов камеры с помощью декодера, поставляемого с Windows, определяются следующим образом:

> Microsoft BarcodeScanner (*название вашей камеры*)

Если у вас есть несколько камер и они встроены в корпус компьютера, имя может различаться между *фронтальной* и *задней* камерами.

> [!NOTE]
> В будущем могут быть выпущены дополнительные программные декодеры с разными схемами именования.

При запуске Девицеватчер (шаг 4) он перебирается по каждому подключенному устройству. Здесь мы добавим доступные Сканеры в коллекцию сканеров штрихкодов и привяжите коллекцию к ListBox.

```csharp
ObservableCollection<BarcodeScannerInfo> barcodeScanners = new ObservableCollection<BarcodeScannerInfo>();

private async void Watcher_Added(DeviceWatcher sender, DeviceInformation args)
{
    await Dispatcher.RunAsync(CoreDispatcherPriority.Normal, () =>
    {
        barcodeScanners.Add(new BarcodeScannerInfo(args.Name, args.Id));

        // Select the first scanner by default.
        if (barcodeScanners.Count == 1)
        {
            ScannerListBox.SelectedIndex = 0;
        }
    });
}
```

При изменении SelectedIndex элемента управления ListBox (первый элемент, выбранный по умолчанию в предыдущем фрагменте), запрашивает сведения об устройстве.

```csharp
private async void ScannerSelection_Changed(object sender, SelectionChangedEventArgs args)
{
    var selectedScannerInfo = (BarcodeScannerInfo)args.AddedItems[0];
    var deviceId = selectedScannerInfo.DeviceId;

    await SelectScannerAsync(deviceId);
}
```

## <a name="step-6-claim-the-camera-barcode-scanner"></a>Шаг 6. Утверждение сканера штрихкодов камеры

Используйте метод [BarcodeScanner.ClaimScannerAsync](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodescanner.claimscannerasync#Windows_Devices_PointOfService_BarcodeScanner_ClaimScannerAsync) для получения права на исключительное использование сканера штрихкодов на базе камеры.

```csharp
private async Task SelectScannerAsync(string scannerDeviceId)
{
    selectedScanner = await BarcodeScanner.FromIdAsync(scannerDeviceId);

    if (selectedScanner != null)
    {
        claimedScanner = await selectedScanner.ClaimScannerAsync();
        if (claimedScanner != null)
        {
            await claimedScanner.EnableAsync();
        }
        else
        {
            rootPage.NotifyUser("Failed to claim the selected barcode scanner", NotifyType.ErrorMessage);
        }
    }
    else
    {
        rootPage.NotifyUser("Failed to create a barcode scanner object", NotifyType.ErrorMessage);
    }
}
```

## <a name="step-7-system-provided-preview"></a>Шаг 7. Предварительная версия предоставлена системой

Предварительное изображение с камеры необходимо, чтобы пользователь мог успешно наводить камеру на штрихкоды.  Windows предоставляет простую предварительную версию камеры, которая запускает диалоговое окно для обычного управления сканером штрихкодов камеры.  Просто вызовите метод [ClaimedBarcodeScanner.ShowVideoPreview](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.claimedbarcodescanner.showvideopreviewasync), чтобы открыть диалоговое окно, и метод [ClaimedBarcodeScanner.HideVideoPreview](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.claimedbarcodescanner.hidevideopreview), чтобы закрыть его после завершения.

> [!TIP]
> О том, как разместить в своем приложении предварительное изображение со сканера штрихкодов на базе камеры, см. в статье [Размещение предварительного изображения](pos-camerabarcode-hosting-preview.md).

## <a name="step-8-initiate-scan"></a>Шаг 8. Начать сканирование

Запускать процесс сканирования можно путем вызова метода [**StartSoftwareTriggerAsync**](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.claimedbarcodescanner.startsoftwaretriggerasync#Windows_Devices_PointOfService_ClaimedBarcodeScanner_StartSoftwareTriggerAsync).

В зависимости от значения [**исдисабледондатарецеивед**](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.claimedbarcodescanner.isdisabledondatareceived#Windows_Devices_PointOfService_ClaimedBarcodeScanner_IsDisabledOnDataReceived) сканер может сканировать только один штрихкод, а затем останавливаться или сканировать непрерывно до вызова [**стопсофтваретригжерасинк**](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.claimedbarcodescanner.stopsoftwaretriggerasync#Windows_Devices_PointOfService_ClaimedBarcodeScanner_StopSoftwareTriggerAsync).

Задайте одно из значений [**IsDisabledOnDataReceived**](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.claimedbarcodescanner.isdisabledondatareceived#Windows_Devices_PointOfService_ClaimedBarcodeScanner_IsDisabledOnDataReceived), чтобы задать нужное поведение сканера при декодировании штрихкода.

| Значение | Описание |
| ----- | ----------- |
| True   | Сканировать только один штрихкод, затем остановиться |
| False  | Сканировать штрихкоды без остановки |

## <a name="see-also"></a>См. также

### <a name="samples"></a>Примеры

- [Пример сканера штрихкодов](https://github.com/microsoft/Windows-universal-samples/tree/master/Samples/BarcodeScanner)
